---
title: Métriques de la plateforme Azure Monitor exportables par le biais des paramètres de diagnostic
description: Liste des métriques disponibles pour chaque type de ressource avec Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 91fc2c4525ee622064520b0098087d54158bbe9e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680695"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Métriques de plateforme Azure Monitor exportables par le biais des paramètres de diagnostic

Azure Monitor fournit des métriques de [plateforme](data-platform-metrics.md) par défaut sans configuration. Il offre plusieurs moyens d’interagir avec les métriques de plateforme, y compris en créant des graphiques dans le portail, en y accédant via l’API REST ou en envoyant des requêtes avec PowerShell ou l’interface CLI. Pour obtenir la liste complète des métriques de plateforme disponibles avec le pipeline de métriques consolidées d’Azure Monitor, consultez [Métriques prises en charge](metrics-supported.md). Pour rechercher ces métriques et y accéder, veuillez utiliser [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). D’autres métriques peuvent être disponibles dans le portail ou via les API héritées.

Vous pouvez exporter les métriques de plateforme à partir du pipeline Azure Monitor vers d’autres emplacements de l’une des deux façons suivantes.
1. Utilisation de [paramètres de diagnostic](diagnostic-settings.md) à envoyer à Log Analytics, Event Hubs ou Stockage Azure
2. Utilisation de l’[API REST pour les métriques](https://docs.microsoft.com/rest/api/monitor/metrics/list)

En raison des subtilités du back-end Azure Monitor, toutes les métriques ne sont pas exportables à l’aide des paramètres de diagnostic. Le tableau ci-dessous liste celles qui peuvent et ne peuvent pas être exportées à l’aide des paramètres de diagnostic.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Changement de comportement pour les valeurs NULL et zéro 
 
Pour les métriques de plateforme qui peuvent être exportées via les paramètres de diagnostic, il existe quelques mesures pour lesquelles Azure Monitor interprète « 0 » comme « Null ». Ceci a provoqué une certaine confusion entre les véritables « 0 » (émis par la ressource) et les « 0 » interprétés (Null). Un changement aura lieu bientôt et les métriques de plateforme exportées via les paramètres de diagnostic n’exporteront plus de « 0 », sauf si elles ont été réellement émises par la ressource sous-jacente. 

> [!CAUTION]
> Le changement de comportement décrit ci-dessus est prévu pour le 1er juin 2020.

Notez ce qui suit :

1.  Si vous supprimez un groupe de ressources ou une ressource spécifique, les données des métriques des ressources affectées ne seront plus envoyées aux destinations d’exportation des paramètres de diagnostic. Autrement dit, elles n’apparaissent plus dans Event Hubs, dans les comptes de stockage et dans les espaces de travail Log Analytics.
2.  Cette amélioration est disponible dans tous les clouds publics et privés.
3.  Cette modification n’a pas d’impact sur le comportement des expériences suivantes : 
   - Journaux de ressources de plateforme exportés via les paramètres de diagnostic
   - Graphiques de métriques dans Metrics Explorer
   - Alertes sur les métriques de plateforme
 
## <a name="metrics-exportable-table"></a>Table exportable de métriques 

Le tableau contient les colonnes suivantes. 
- Exportable par le biais des paramètres de diagnostic ? 
- Affecté par NULL/0 
- ResourceType 
- Métrique 
- Nom d’affichage de la métrique
- Unité 
- AggregationType


> [!NOTE]
> Le tableau ci-dessous peut comporter une barre de défilement horizontale en bas. Si vous pensez qu’il manque des informations, vérifiez que la barre de défilement est tout à fait à gauche.  


| Exportable par le biais des paramètres de diagnostic ?  | Émet déjà des valeurs NULL |  ResourceType  |  Métrique  |  Nom d’affichage de la métrique  |  Unité  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| ****Oui****  | Non |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  Mémoire : prix actuel du nettoyage  |  Count  |  Average | 
| ****Oui****  | Non |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Mémoire : mémoire de nettoyage non réductible  |  Octets  |  Average | 
| ****Oui****  | Non |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Mémoire : mémoire de nettoyage réductible  |  Octets  |  Average | 
| ****Oui****  | Non |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Threads : threads occupés du pool de commandes  |  Count  |  Average | 
| ****Oui****  | Non |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Threads : threads inactifs du pool de commandes  |  Count  |  Average | 
| ****Oui****  | Non |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Longueur de la file d’attente des travaux du pool de commandes  |  Count  |  Average | 
| ****Oui****  | Non |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  Connexion : Connexions en cours  |  Count  |  Average | 
| ****Oui****  | Non |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Sessions utilisateur actuelles  |  Count  |  Average | 
| ****Oui****  | Non |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Threads : threads occupés d'analyse longue  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Threads : threads inactifs d'analyse longue  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Threads : longueur de la file d'attente des travaux d'analyse longue  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  Mémoire du moteur M  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  Octets privés du moteur M  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  QPU du moteur M  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  Octets virtuels du moteur M  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Mémoire  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Vidage de mémoire  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Mémoire : limite inconditionnelle de la mémoire  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Mémoire : limite haute de la mémoire  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Mémoire : limite basse de la mémoire  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Mémoire : limite de la mémoire VertiPaq  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  Mémoire : Utilisation de la mémoire  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Octets privés  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Threads : threads des travaux d'E/S occupés du pool de traitement  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Threads : threads autres que les threads d'E/S occupés du pool de traitement  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Threads : threads des travaux d'E/S inactifs du pool de traitement  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Threads : threads autres que les threads d'E/S inactifs du pool de traitement  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Threads : longueur de la file des travaux d'E/S du pool de traitement  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Longueur de la file d’attente des travaux du pool de traitement  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  Threads occupés du pool de threads de requêtes  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Threads : threads inactifs du pool de requêtes  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Threads : longueur de la file d'attente des travaux du pool de requêtes  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  Quota  |  Mémoire : Quota  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  Mémoire : quota bloqué  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  Traitement : lignes converties par seconde  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  Traitement : lignes lues par seconde  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  Traitement : lignes écrites par seconde  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Threads : threads occupés d'analyse courte  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Threads : threads inactifs d'analyse courte  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Threads : longueur de la file d'attente des travaux d'analyse courte  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Connexions réussies par seconde  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  Nombre total d’échecs de connexion  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Nombre total de demandes de connexion  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Mémoire : mémoire non paginée VertiPaq  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Mémoire : mémoire paginée VertiPaq  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Octets virtuels  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Durées des demandes back-end  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ApiManagement/service  |  Capacité  |  Capacité  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  Duration  |  Durée globale des demandes de passerelle  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Événements EventHub supprimés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Événements EventHub rejetés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Événements EventHub réussis  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Événements EventHub limités  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Événements EventHub expirés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Taille des événements EventHub  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Nombre total d'événements EventHub  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Événements EventHub non réussis  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Demandes de la passerelle ayant échoué (déprécié)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Autres demandes de la passerelle (déprécié)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  Demandes  |  Demandes  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Demandes de la passerelle ayant abouti (déprécié)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Nombre total de demandes de la passerelle (déprécié)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Demandes de la passerelle non autorisées (déprécié)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  AppCpuUsagePercentage  |  Pourcentage d’utilisation du processeur d’application  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  AppMemoryCommitted  |  Mémoire d’application affectée  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  AppMemoryMax  |  Mémoire d’application maximale  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  AppMemoryUsed  |  Mémoire d’application utilisée  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  GCPauseTotalCount  |  Nombre d’interruptions du GC  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  GCPauseTotalTime  |  Durée totale de pause du GC  |  Millisecondes  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  MaxOldGenMemoryPoolBytes  |  Taille maximale des données d’ancienne génération disponibles  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  OldGenMemoryPoolBytes  |  Taille des données d’ancienne génération  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  OldGenPromotedBytes  |  Promouvoir à la taille des données d’ancienne génération  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  SystemCpuUsagePercentage  |  Pourcentage d’utilisation du processeur système  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatErrorCount  |  Erreur globale Tomcat  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatReceivedBytes  |  Nombre total d’octets reçus par Tomcat  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatRequestMaxTime  |  Durée maximale des demandes Tomcat  |  Millisecondes  |  Maximale | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalCount  |  Nombre total de demandes Tomcat  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalTime  |  Durée totale des demandes Tomcat  |  Millisecondes  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatResponseAvgTime  |  Durée moyenne des demandes Tomcat  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatSentBytes  |  Nombre total d’octets envoyés par Tomcat  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveCurrentCount  |  Nombre de sessions actives Tomcat  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveMaxCount  |  Nombre maximal de sessions actives Tomcat  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatSessionAliveMaxTime  |  Durée maximale des sessions actives Tomcat  |  Millisecondes  |  Maximale | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatSessionCreatedCount  |  Nombre de sessions Tomcat créées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatSessionExpiredCount  |  Nombre de sessions Tomcat ayant expiré  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  TomcatSessionRejectedCount  |  Nombre de sessions Tomcat rejetées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.AppPlatform/Spring  |  YoungGenPromotedBytes  |  Promouvoir à la taille des données de nouvelle génération  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Nombre total de travaux  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  Nombre total d’exécutions de déploiement de mises à jour de machines  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  Nombre total d’exécutions de déploiement de mises à jour  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Nombre de cœurs dédiés  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Nombre de nœuds créés  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Nombre de nœuds inactifs  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Événements de fin de suppression de travail  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Événements de démarrage de suppression de travail  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Événements de fin de désactivation de travail  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Événements de démarrage de désactivation de travail  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Événements de démarrage de travail  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Événements de fin de travail terminé  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Événements de démarrage de fin de travail  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Nombre de nœuds quittant le pool  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  Nombre de cœurs à priorité basse  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Nombre de nœuds hors ligne  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Événements de création de pool  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Événements de suppression de pool terminés  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Événements de démarrage de suppression de pool  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Événements de fin de redimensionnement de pool  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Événements de démarrage de redimensionnement de pool  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Nombre de nœuds reportés  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Nombre de nœuds en cours de redémarrage  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Nombre de nœuds en cours de réimageage  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Nombre de nœuds en cours d’exécution  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Nombre de nœuds de départ  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Nombre de nœuds pour lesquels le démarrage d’une tâche a échoué  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Événements de fin de tâche  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Événements d’échec de tâches  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Événements de lancement de tâche  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Nombre de nœuds à priorité basse  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Nombre de nœuds dédiés  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Nombre de nœuds inutilisables  |  Count  |  Total | 
| Non  | Non |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Nombre de nœuds en attente de démarrage de tâche  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Cœurs actifs  |  Cœurs actifs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Nœuds actifs  |  Nœuds actifs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Cœurs inactifs  |  Cœurs inactifs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Nœuds inactifs  |  Nœuds inactifs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Travail effectué  |  Travail effectué  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Travail envoyé  |  Travail envoyé  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Cœurs sortants  |  Cœurs sortants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Nœuds sortants  |  Nœuds sortants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Cœurs réquisitionnés  |  Cœurs réquisitionnés  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Nœuds reportés  |  Nœuds reportés  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Pourcentage d’utilisation du quota  |  Pourcentage d’utilisation du quota  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Nombre total de cœurs  |  Nombre total de cœurs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Nombre total de nœuds  |  Nombre total de nœuds  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Cœurs inutilisables  |  Cœurs inutilisables  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.BatchAI/workspaces  |  Nœuds inutilisables  |  Nœuds inutilisables  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  ConnectionAccepted  |  Connexions acceptées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  ConnectionActive  |  Connexions actives  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  ConnectionHandled  |  Connexions gérées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  Pourcentage d’utilisation du processeur  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  E/S de lecture d’octets  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  E/S d’écriture d’octets  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  MemoryLimit  |  Limite de mémoire  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsage  |  Utilisation de la mémoire  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Pourcentage d’utilisation de la mémoire  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  PendingTransactions  |  Transactions en attente  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  ProcessedBlocks  |  Blocs traités  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  ProcessedTransactions  |  Transactions traitées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  QueuedTransactions  |  Transactions en file d’attente  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  RequestHandled  |  Demandes traitées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Blockchain/blockchainMembers  |  StorageUsage  |  Utilisation du stockage  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits  |  Présences dans le cache  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits0  |  Présences dans le cache (Shard 0)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits1  |  Présences dans le cache (Shard 1)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits2  |  Présences dans le cache (Shard 2)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits3  |  Présences dans le cache (Shard 3)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits4  |  Présences dans le cache (Shard 4)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits5  |  Présences dans le cache (Shard 5)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits6  |  Présences dans le cache (Shard 6)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits7  |  Présences dans le cache (Shard 7)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits8  |  Présences dans le cache (Shard 8)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachehits9  |  Présences dans le cache (Shard 9)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheLatency  |  Latence de cache en microsecondes (préversion)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses  |  Absences dans le cache  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses0  |  Absences dans le cache (Shard 0)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses1  |  Absences dans le cache (Shard 1)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses2  |  Absences dans le cache (Shard 2)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses3  |  Absences dans le cache (Shard 3)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses4  |  Absences dans le cache (Shard 4)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses5  |  Absences dans le cache (Shard 5)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses6  |  Absences dans le cache (Shard 6)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses7  |  Absences dans le cache (Shard 7)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses8  |  Absences dans le cache (Shard 8)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cachemisses9  |  Absences dans le cache (Shard 9)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead  |  Lecture du cache  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead0  |  Cache de lecture (Shard 0)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead1  |  Lecture du cache (Shard 1)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead2  |  Lecture du cache (Shard 2)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead3  |  Lecture du cache (Shard 3)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead4  |  Lecture du cache (Shard 4)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead5  |  Lecture du cache (Shard 5)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead6  |  Lecture du cache (Shard 6)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead7  |  Lecture du cache (Shard 7)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead8  |  Lecture du cache (Shard 8)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheRead9  |  Cache de lecture (Shard 9)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite  |  Cache d’écriture  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite0  |  Cache d’écriture (Shard 0)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite1  |  Cache d’écriture (Shard 1)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite2  |  Cache d’écriture (Shard 2)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite3  |  Cache d’écriture (Shard 3)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite4  |  Cache d’écriture (Shard 4)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite5  |  Cache d’écriture (Shard 5)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite6  |  Cache d’écriture (Shard 6)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite7  |  Cache d’écriture (Shard 7)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite8  |  Cache d’écriture (Shard 8)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  cacheWrite9  |  Cache d’écriture (Shard 9)  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients  |  Clients connectés  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients0  |  Clients connectés (Shard 0)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients1  |  Clients connectés (Shard 1)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients2  |  Clients connectés (Shard 2)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients3  |  Clients connectés (Shard 3)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients4  |  Clients connectés (Shard 4)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients5  |  Clients connectés (Shard 5)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients6  |  Clients connectés (Shard 6)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients7  |  Clients connectés (Shard 7)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients8  |  Clients connectés (Shard 8)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  connectedclients9  |  Clients connectés (Shard 9)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  erreurs  |  Erreurs  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys  |  Clés exclues  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys0  |  Clés exclues (Shard 0)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys1  |  Clés exclues (Shard 1)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys2  |  Clés exclues (Shard 2)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys3  |  Clés exclues (Shard 3)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys4  |  Clés exclues (Shard 4)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys5  |  Clés exclues (Shard 5)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys6  |  Clés exclues (Shard 6)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys7  |  Clés exclues (Shard 7)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys8  |  Clés exclues (Shard 8)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  evictedkeys9  |  Clés exclues (Shard 9)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys  |  Clés expirées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys0  |  Clés expirées (Shard 0)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys1  |  Clés expirées (Shard 1)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys2  |  Clés expirées (Shard 2)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys3  |  Clés expirées (Shard 3)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys4  |  Clés expirées (Shard 4)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys5  |  Clés expirées (Shard 5)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys6  |  Clés expirées (Shard 6)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys7  |  Clés expirées (Shard 7)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys8  |  Clés expirées (Shard 8)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  expiredkeys9  |  Clés expirées (Shard 9)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands  |  Gets  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands0  |  Gets (Shard 0)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands1  |  Gets (Shard 1)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands2  |  Gets (Shard 2)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands3  |  Gets (Shard 3)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands4  |  Gets (Shard 4)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands5  |  Gets (Shard 5)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands6  |  Gets (Shard 6)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands7  |  Gets (Shard 7)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands8  |  Gets (Shard 8)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  getcommands9  |  Gets (Shard 9)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond  |  Opérations par seconde  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond0  |  Opérations par seconde (Partition 0)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond1  |  Opérations par seconde (Partition 1)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond2  |  Opérations par seconde (Partition 2)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond3  |  Opérations par seconde (Partition 3)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond4  |  Opérations par seconde (Partition 4)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond5  |  Opérations par seconde (Partition 5)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond6  |  Opérations par seconde (Partition 6)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond7  |  Opérations par seconde (Partition 7)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond8  |  Opérations par seconde (Partition 8)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  operationsPerSecond9  |  Opérations par seconde (Partition 9)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime  |  UC  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime0  |  UC (Shard 0)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime1  |  UC (Shard 1)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime2  |  UC (Shard 2)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime3  |  UC (Shard 3)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime4  |  UC (Shard 4)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime5  |  UC (Shard 5)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime6  |  UC (Shard 6)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime7  |  UC (Shard 7)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime8  |  UC (Shard 8)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  percentProcessorTime9  |  UC (Shard 9)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad  |  Charge du serveur  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad0  |  Charge du serveur (Shard 0)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad1  |  Charge du serveur (Shard 1)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad2  |  Charge du serveur (Shard 2)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad3  |  Charge du serveur (Shard 3)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad4  |  Charge du serveur (Shard 4)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad5  |  Charge du serveur (Shard 5)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad6  |  Charge du serveur (Shard 6)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad7  |  Charge du serveur (Shard 7)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad8  |  Charge du serveur (Shard 8)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  serverLoad9  |  Charge du serveur (Shard 9)  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands  |  Jeux  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands0  |  Sets (Shard 0)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands1  |  Sets (Shard 1)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands2  |  Sets (Shard 2)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands3  |  Sets (Shard 3)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands4  |  Sets (Shard 4)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands5  |  Sets (Shard 5)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands6  |  Sets (Shard 6)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands7  |  Sets (Shard 7)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands8  |  Sets (Shard 8)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  setcommands9  |  Sets (Shard 9)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Total des opérations  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Total des opérations (Shard 0)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Total des opérations (Shard 1)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Total des opérations (Shard 2)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Total des opérations (Shard 3)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Total des opérations (Shard 4)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Total des opérations (Shard 5)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Total des opérations (Shard 6)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Total des opérations (Shard 7)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Total des opérations (Shard 8)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Total des opérations (Shard 9)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys  |  Nombre total de clés  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys0  |  Nombre total de clés (Shard 0)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys1  |  Nombre total de clés (Shard 1)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys2  |  Nombre total de clés (Shard 2)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys3  |  Nombre total de clés (Shard 3)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys4  |  Nombre total de clés (Shard 4)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys5  |  Nombre total de clés (Shard 5)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys6  |  Nombre total de clés (Shard 6)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys7  |  Nombre total de clés (Shard 7)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys8  |  Nombre total de clés (Shard 8)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  totalkeys9  |  Nombre total de clés (Shard 9)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory  |  Mémoire utilisée  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory0  |  Mémoire utilisée (Shard 0)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory1  |  Mémoire utilisée (Shard 1)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory2  |  Mémoire utilisée (Shard 2)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory3  |  Mémoire utilisée (Shard 3)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory4  |  Mémoire utilisée (Shard 4)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory5  |  Mémoire utilisée (Shard 5)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory6  |  Mémoire utilisée (Shard 6)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory7  |  Mémoire utilisée (Shard 7)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory8  |  Mémoire utilisée (Shard 8)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemory9  |  Mémoire utilisée (Shard 9)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemorypercentage  |  Pourcentage de mémoire utilisé  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss  |  Taille de la mémoire résidente utilisée  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss0  |  Mémoire résidente utilisée (Shard 0)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss1  |  Mémoire résidente utilisée (Shard 1)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss2  |  Mémoire résidente utilisée (Shard 2)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss3  |  Mémoire résidente utilisée (Shard 3)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss4  |  Mémoire résidente utilisée (Shard 4)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss5  |  Mémoire résidente utilisée (Shard 5)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss6  |  Mémoire résidente utilisée (Shard 6)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss7  |  Mémoire résidente utilisée (Shard 7)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss8  |  Mémoire résidente utilisée (Shard 8)  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Cache/redis  |  usedmemoryRss9  |  Mémoire résidente utilisée (Shard 9)  |  Octets  |  Maximale | 
| Non  | Non |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Bytes/Sec  |  Lecture du disque  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Bytes/Sec  |  Écriture sur le disque  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Network In  |  Network In  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Network Out  |  Network Out  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Percentage CPU  |  Percentage CPU  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.ClassicCompute/virtualMachines  |  Disk Read Bytes/Sec  |  Lecture du disque  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicCompute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.ClassicCompute/virtualMachines  |  Disk Write Bytes/Sec  |  Écriture sur le disque  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicCompute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicCompute/virtualMachines  |  Network In  |  Network In  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicCompute/virtualMachines  |  Network Out  |  Network Out  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicCompute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts  |  Sortie  |  Sortie  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts  |  Entrée  |  Entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Latence E2E de réussite  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Latence du serveur avec requête réussie  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts  |  Transactions  |  Transactions  |  Count  |  Total | 
| Non  | Non |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  Capacité utilisée  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Capacité d’objet blob  |  Octets  |  Average | 
| Non  | Non |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Nombre d’objets blob  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  Nombre de conteneurs d’objets blob  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Sortie  |  Sortie  |  Octets  |  Total | 
| Non  | Non |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Capacité d'index  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Entrée  |  Entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Latence E2E de réussite  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Latence du serveur avec requête réussie  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Transactions  |  Transactions  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Sortie  |  Sortie  |  Octets  |  Total | 
| Non  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCapacity  |  Capacité de fichiers  |  Octets  |  Average | 
| Non  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCount  |  Nombre de fichiers  |  Count  |  Average | 
| Non  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Nombre de partages de fichiers  |  Count  |  Average | 
| Non  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  Taille du quota de partage de fichiers  |  Octets  |  Average | 
| Non  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Nombre d’instantanés de partage de fichiers  |  Count  |  Average | 
| Non  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Taille d’instantané de partage de fichiers  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Entrée  |  Entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Latence E2E de réussite  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Latence du serveur avec requête réussie  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Transactions  |  Transactions  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Sortie  |  Sortie  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Entrée  |  Entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Capacité de la file d’attente  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCount  |  Nombre de files d’attente  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Nombre de messages dans la file d’attente  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Latence E2E de réussite  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Latence du serveur avec requête réussie  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Transactions  |  Transactions  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Sortie  |  Sortie  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Entrée  |  Entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Latence E2E de réussite  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Latence du serveur avec requête réussie  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableCapacity  |  Capacité de la table  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableCount  |  Nombre de tables  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Nombre d’entités de table  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Transactions  |  Transactions  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  Appels bloqués  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  CharactersTrained  |  Caractères formés  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  Caractères traduits  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  Erreurs de client  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  DataIn  |  Données entrantes  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  DataOut  |  Données sortantes  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  Latence  |  Latence  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  Erreurs de serveur  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  Durée de la session vocale  |  Secondes  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  Appels réussis  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  Nombre total d’appels  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  Nombre total d’erreurs  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  Total d’appels de jeton  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  Nombre total de transactions  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Crédits de processeur consommés  |  Crédits de processeur consommés  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Crédits de processeurs restants  |  Crédits de processeurs restants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Longueur de file d’attente du disque de données  |  Longueur de file d’attente du disque de données (préversion)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Octets lus/s sur disque de données  |  Octets lus/s sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Opérations de lecture/s sur disque de données  |  Opérations de lecture/s sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Octets écrits/s sur disque de données  |  Octets/s écrits sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Opérations d’écriture/s sur disque de données  |  Opérations d’écriture/s sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Disk Read Bytes  |  Disk Read Bytes  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Flux entrants  |  Flux entrants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Taux de création maximal de flux entrants  |  Taux de création maximal de flux entrants (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Network In  |  Octets entrants réseau facturables (déprécié)  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Octets entrants réseau totaux  |  Octets entrants réseau totaux  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Network Out  |  Octets sortants réseau facturables (déprécié)  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Octets sortants réseau totaux  |  Octets sortants réseau totaux  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Longueur de file d’attente du disque du système d’exploitation  |  Longueur de file d’attente du disque de données (préversion)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Octets lus/s sur disque du système d’exploitation  |  Octets lus/s sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Opérations de lecture/s sur disque du système d’exploitation  |  Opérations de lecture/s sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Octets/s écrits sur disque du système d’exploitation  |  Octets/s écrits sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Opérations d’écriture/s sur disque du système d’exploitation  |  Opérations d’écriture/s sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  QD sur système d’exploitation par disque  |  QD du disque du système d'exploitation (déconseillé)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Octets lus/s sur système d’exploitation par disque  |  Octets lus/s sur système d'exploitation par disque (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Opérations de lecture/s sur système d’exploitation par disque  |  Opérations de lecture/s sur disque du système d'exploitation (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Octets écrits/s sur système d’exploitation par disque  |  Octets écrits/s sur disque du système d'exploitation (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Opérations d’écriture/s sur système d’exploitation par disque  |  Opérations d’écriture/s sur disque du système d'exploitation (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Flux sortants  |  Flux sortants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Taux de création maximal de flux sortants  |  Taux de création maximal de flux sortants (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  QD par disque  |  QD par disque de données (déconseillé)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Octets lus/s par disque  |  Octets lus/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Opérations de lecture/s par disque  |  Opérations de lecture/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Octets écrits/s par disque  |  Octets écrits/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Opérations d’écriture/s par disque  |  Opérations d’écriture/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Accès en lecture dans le cache de disque de données Premium  |  Accès en lecture dans le cache de disque de données Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Échec de lecture dans le cache de disque de données Premium  |  Échec de lecture dans le cache de disque de données Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Accès en lecture dans le cache de disque OS Premium  |  Accès en lecture dans le cache de disque OS Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachines  |  Échec de lecture dans le cache de disque OS Premium  |  Échec de lecture dans le cache de disque OS Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Crédits de processeur consommés  |  Crédits de processeur consommés  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Crédits de processeurs restants  |  Crédits de processeurs restants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Longueur de file d’attente du disque de données  |  Longueur de file d’attente du disque de données (préversion)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Octets lus/s sur disque de données  |  Octets lus/s sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Opérations de lecture/s sur disque de données  |  Opérations de lecture/s sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Octets écrits/s sur disque de données  |  Octets/s écrits sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Opérations d’écriture/s sur disque de données  |  Opérations d’écriture/s sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Read Bytes  |  Disk Read Bytes  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Write Bytes  |  Disk Write Bytes  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Flux entrants  |  Flux entrants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Taux de création maximal de flux entrants  |  Taux de création maximal de flux entrants (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Network In  |  Octets entrants réseau facturables (déprécié)  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Octets entrants réseau totaux  |  Octets entrants réseau totaux  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Network Out  |  Octets sortants réseau facturables (déprécié)  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Octets sortants réseau totaux  |  Octets sortants réseau totaux  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Longueur de file d’attente du disque du système d’exploitation  |  Longueur de file d’attente du disque de données (préversion)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Octets lus/s sur disque du système d’exploitation  |  Octets lus/s sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Opérations de lecture/s sur disque du système d’exploitation  |  Opérations de lecture/s sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Octets/s écrits sur disque du système d’exploitation  |  Octets/s écrits sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Opérations d’écriture/s sur disque du système d’exploitation  |  Opérations d’écriture/s sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  QD sur système d’exploitation par disque  |  QD du disque du système d'exploitation (déconseillé)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Octets lus/s sur système d’exploitation par disque  |  Octets lus/s sur système d'exploitation par disque (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Opérations de lecture/s sur système d’exploitation par disque  |  Opérations de lecture/s sur disque du système d'exploitation (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Octets écrits/s sur système d’exploitation par disque  |  Octets écrits/s sur disque du système d'exploitation (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Opérations d’écriture/s sur système d’exploitation par disque  |  Opérations d’écriture/s sur disque du système d'exploitation (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Flux sortants  |  Flux sortants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Taux de création maximal de flux sortants  |  Taux de création maximal de flux sortants (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  QD par disque  |  QD par disque de données (déconseillé)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Octets lus/s par disque  |  Octets lus/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Opérations de lecture/s par disque  |  Opérations de lecture/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Octets écrits/s par disque  |  Octets écrits/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Opérations d’écriture/s par disque  |  Opérations d’écriture/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Percentage CPU  |  Percentage CPU  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Accès en lecture dans le cache de disque de données Premium  |  Accès en lecture dans le cache de disque de données Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Échec de lecture dans le cache de disque de données Premium  |  Échec de lecture dans le cache de disque de données Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Accès en lecture dans le cache de disque OS Premium  |  Accès en lecture dans le cache de disque OS Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets  |  Échec de lecture dans le cache de disque OS Premium  |  Échec de lecture dans le cache de disque OS Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Crédits de processeur consommés  |  Crédits de processeur consommés  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Crédits de processeurs restants  |  Crédits de processeurs restants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Longueur de file d’attente du disque de données  |  Longueur de file d’attente du disque de données (préversion)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Octets lus/s sur disque de données  |  Octets lus/s sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Opérations de lecture/s sur disque de données  |  Opérations de lecture/s sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Octets écrits/s sur disque de données  |  Octets/s écrits sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Opérations d’écriture/s sur disque de données  |  Opérations d’écriture/s sur disque de données (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Read Bytes  |  Disk Read Bytes  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Flux entrants  |  Flux entrants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Taux de création maximal de flux entrants  |  Taux de création maximal de flux entrants (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Network In  |  Octets entrants réseau facturables (déprécié)  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Octets entrants réseau totaux  |  Octets entrants réseau totaux  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Network Out  |  Octets sortants réseau facturables (déprécié)  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Octets sortants réseau totaux  |  Octets sortants réseau totaux  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Longueur de file d’attente du disque du système d’exploitation  |  Longueur de file d’attente du disque de données (préversion)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Octets lus/s sur disque du système d’exploitation  |  Octets lus/s sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Opérations de lecture/s sur disque du système d’exploitation  |  Opérations de lecture/s sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Octets/s écrits sur disque du système d’exploitation  |  Octets/s écrits sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Opérations d’écriture/s sur disque du système d’exploitation  |  Opérations d’écriture/s sur disque du système d’exploitation (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  QD sur système d’exploitation par disque  |  QD du disque du système d'exploitation (déconseillé)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Octets lus/s sur système d’exploitation par disque  |  Octets lus/s sur système d'exploitation par disque (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Opérations de lecture/s sur système d’exploitation par disque  |  Opérations de lecture/s sur disque du système d'exploitation (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Octets écrits/s sur système d’exploitation par disque  |  Octets écrits/s sur disque du système d'exploitation (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Opérations d’écriture/s sur système d’exploitation par disque  |  Opérations d’écriture/s sur disque du système d'exploitation (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Flux sortants  |  Flux sortants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Taux de création maximal de flux sortants  |  Taux de création maximal de flux sortants (préversion)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  QD par disque  |  QD par disque de données (déconseillé)  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Octets lus/s par disque  |  Octets lus/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Opérations de lecture/s par disque  |  Opérations de lecture/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Octets écrits/s par disque  |  Octets écrits/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Opérations d’écriture/s par disque  |  Opérations d’écriture/s sur disque de données (déconseillé)  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Accès en lecture dans le cache de disque de données Premium  |  Accès en lecture dans le cache de disque de données Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Échec de lecture dans le cache de disque de données Premium  |  Échec de lecture dans le cache de disque de données Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Accès en lecture dans le cache de disque OS Premium  |  Accès en lecture dans le cache de disque OS Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Échec de lecture dans le cache de disque OS Premium  |  Échec de lecture dans le cache de disque OS Premium (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  Utilisation du processeur  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  Utilisation de la mémoire  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Octets réseau reçus par seconde  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Octets réseau transmis par seconde  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.ContainerRegistry/registries  |  RunDuration  |  Durée d’exécution  |  Millisecondes  |  Total | 
| **Oui**  | Non |  Microsoft.ContainerRegistry/registries  |  SuccessfulPullCount  |  Nombre d'extractions réussies  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.ContainerRegistry/registries  |  SuccessfulPushCount  |  Nombre d'envois (push) réussis  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.ContainerRegistry/registries  |  TotalPullCount  |  Nombre total d'extractions  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.ContainerRegistry/registries  |  TotalPushCount  |  Nombre total d'envois (push)  |  Count  |  Average | 
| Non  | Non |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Nombre total de cœurs d’unité centrale disponibles dans un cluster géré  |  Count  |  Average | 
| Non  | Non |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Quantité totale de mémoire disponible dans un cluster géré  |  Octets  |  Average | 
| Non  | Non |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  États pour les différentes conditions de nœud  |  Count  |  Average | 
| Non  | Non |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Nombre de pods par phase  |  Count  |  Average | 
| Non  | Non |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Nombre de pods en état Prêt  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  AvailableCapacity  |  Capacité disponible  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Octets cloud chargés (appareil)  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Octets cloud chargés (partage)  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Débit de téléchargement cloud  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Débit de téléchargement cloud (partage)  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Débit de chargement cloud  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Débit de chargement cloud (partage)  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Computing en périphérie - Utilisation de la mémoire  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Computing en périphérie - Pourcentage du processeur  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Débit de lecture (réseau)  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Débit d’écriture (réseau)  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  TotalCapacity  |  Capacité totale  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.DataFactory/datafactories  |  FailedRuns  |  Exécutions échouées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns  |  Exécutions réussies  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  ActivityCancelledRuns  |  Métriques d’exécutions d’activité annulées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  ActivityFailedRuns  |  Métriques d’exécutions d’activité ayant échoué  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  ActivitySucceededRuns  |  Métriques d’exécutions d’activité ayant abouti  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  FactorySizeInGbUnits  |  Taille de fabrique totale (unité Go)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAvailableMemory  |  Mémoire disponible du runtime d’intégration  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAverageTaskPickupDelay  |  Durée de la file d’attente du runtime d’intégration  |  Secondes  |  Average | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  IntegrationRuntimeCpuPercentage  |  Utilisation du processeur du runtime d’intégration  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  IntegrationRuntimeQueueLength  |  Longueur de la file d’attente du runtime d’intégration  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  MaxAllowedFactorySizeInGbUnits  |  Taille maximale de fabrique autorisée (unité Go)  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  MaxAllowedResourceCount  |  Nombre maximal d’entités autorisées  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  PipelineCancelledRuns  |  Métriques d’exécutions de pipeline annulées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  PipelineFailedRuns  |  Métriques d’exécutions de pipeline ayant échoué  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  PipelineSucceededRuns  |  Métriques d’exécutions de pipeline ayant abouti  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  ResourceCount  |  Nombre total d’entités  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  TriggerCancelledRuns  |  Métriques d’exécutions de déclencheur annulées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  TriggerFailedRuns  |  Métriques d’exécutions de déclencheur ayant échoué  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataFactory/factories  |  TriggerSucceededRuns  |  Métriques d’exécutions de déclencheur ayant abouti  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Durée AU d’annulation  |  Secondes  |  Total | 
| **Oui**  | Non |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Durée AU d’échec  |  Secondes  |  Total | 
| **Oui**  | Non |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Durée AU de réussite  |  Secondes  |  Total | 
| **Oui**  | Non |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Travaux annulés  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Travaux en échec  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Travaux réussis  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataLakeStore/accounts  |  DataRead  |  Données lues  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  Données écrites  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  Demandes de lecture  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  Stockage total  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  Demandes d’écriture  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Connexions actives  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Stockage de sauvegarde utilisé  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  Connexions ayant échoué  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  Pourcentage d’UC  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  Pourcentage d’E/S  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  Pourcentage de mémoire  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Network Out  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Network In  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  Décalage de la réplication en secondes  |  Count  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  Limite de stockage du journal du serveur  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  Pourcentage de stockage du journal du serveur  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  Stockage du journal du serveur utilisé  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  Limite de stockage  |  Octets  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  Pourcentage de stockage  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  Stockage utilisé  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  active_connections  |  Connexions actives  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Stockage de sauvegarde utilisé  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  Connexions ayant échoué  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  Pourcentage d’UC  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  Pourcentage d’E/S  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  Pourcentage de mémoire  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Network Out  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Network In  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  Décalage de la réplication en secondes  |  Count  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  Limite de stockage du journal du serveur  |  Octets  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  Pourcentage de stockage du journal du serveur  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  Stockage du journal du serveur utilisé  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  Limite de stockage  |  Octets  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  Pourcentage de stockage  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DBforMySQL/servers  |  storage_used  |  Stockage utilisé  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Connexions actives  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Stockage de sauvegarde utilisé  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Connexions ayant échoué  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  Pourcentage d’UC  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  Pourcentage d’E/S  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Pourcentage de mémoire  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Network Out  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Network In  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Retard maximum entre réplicas  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Retard du réplica  |  Secondes  |  Maximale | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Limite de stockage du journal du serveur  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Pourcentage de stockage du journal du serveur  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Stockage du journal du serveur utilisé  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Limite de stockage  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Pourcentage de stockage  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Stockage utilisé  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Connexions actives  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  Pourcentage d’UC  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/serversv2  |  iops  |  E/S par seconde  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Pourcentage de mémoire  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Network Out  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Network In  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Pourcentage de stockage  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Stockage utilisé  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/Account  |  digitaltwins.telemetry.nodes  |  Espace réservé des données de télémétrie des nœuds Digital Twins  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Messages cloud vers appareil abandonnés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Remises de messages cloud vers appareil terminées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Messages cloud vers appareil rejetés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  Appels de méthode directe en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Taille de demande des appels de méthode directe  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  Taille de réponse des appels de méthode directe  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Appels de méthode directe réussis  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Lectures de représentations de serveur principal en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Taille de la réponse des lectures de représentations de serveur principal  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Lectures de représentations réussies de serveur principal  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Mises à jour de représentations de serveur principal en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Taille des mises à jour de représentations de serveur principal  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Mises à jour de représentations réussies de serveur principal  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  Messages cloud vers appareil ayant expiré (préversion)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  configurations  |  Métriques de configuration  |  Count  |  Total | 
| Non  | Non |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Appareils connectés (préversion)  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Routage : messages remis à des messages/événements  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Routage : messages remis à Event Hub  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Routage : messages remis à la file d’attente Service Bus  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Routage : messages remis à la rubrique Service Bus  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Routage : messages remis au stockage  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  Routage : objets blob remis au stockage  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Routage : données remises au stockage  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Routage : latence des messages de messages/d’événements  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Routage : latence des messages d’Event Hub  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Routage : latence des messages de la file d’attente Service Bus  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Routage : latence des messages de la rubrique Service Bus  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  Routage : latence des messages du stockage  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Routage : messages de télémétrie annulés   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Routage : messages remis à l’itinéraire de secours  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Routage : messages de télémétrie incompatibles  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Routage : messages de télémétrie orphelins   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Routage : messages de télémétrie remis  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Tentatives d’envoi de message de télémétrie  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Nombre d’erreurs de limitation  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Messages de télémétrie envoyés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Lectures de représentations d’appareils en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Taille de la réponse des lectures de représentations des appareils  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Lectures de représentations réussies d’appareils  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Mises à jour de représentations d’appareils en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Taille des mises à jour de représentations d’appareils  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Mises à jour de représentations réussies d’appareils  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Nombre total de messages utilisés  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Utilisation totale des données d’appareil  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Utilisation totale des données d’appareil (préversion)  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Appareils connectés (déconseillé)   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Nombre total d’appareils (déconseillé)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Remises Event Grid (préversion)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Latence d’Event Grid (préversion)  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Annulations de travaux en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Annulations de travaux réussies  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.completed  |  Travaux terminés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Créations des travaux d’appel de méthode en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Créations réussies des travaux d’appel de méthode  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Créations des travaux de mises à jour de représentations en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Créations réussies des travaux de mises à jour de représentations  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  Travaux en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  Appels en échec pour répertorier les travaux  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Appels réussis pour répertorier les travaux  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Requêtes de travaux en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Requêtes de travaux réussies  |  Count  |  Total | 
| Non  | Non |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Nombre total d’appareils (préversion)  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Requêtes de représentations en échec  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Taille du résultat des requêtes de représentations  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Requêtes de représentations réussies  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Tentatives d’attestation  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  Appareils attribués  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  Tentatives d’enregistrement  |  Count  |  Total | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  Stockage disponible  |  Octets  |  Total | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Fermetures de connexion Cassandra  |  Count  |  Total | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Frais de requête Cassandra  |  Count  |  Total | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Requêtes Cassandra  |  Count  |  Count | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  Utilisation des données  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Count  |  Count | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Nombre de documents  |  Count  |  Total | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Quota de document  |  Octets  |  Total | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Utilisation d'index  |  Octets  |  Total | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  Demandes de métadonnées  |  Count  |  Count | 
| **Oui**  | **Oui** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Frais des requêtes Mongo  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Requêtes Mongo  |  Count  |  Count | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Taux de demandes Mongo  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Taux de requête de suppression Mongo  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Taux de requête d’insertion Mongo  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Taux de requêtes d’interrogation Mongo  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Taux de demande de mise à jour Mongo  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Débit approvisionné  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  Latence de réplication P99  |  Millisecondes  |  Average | 
| Non  | Non |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  Disponibilité des services  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Total de requêtes  |  Count  |  Count | 
| **Oui**  | **Oui** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Unités de requête totales  |  Count  |  Total | 
| Non  | Non |  Microsoft.EnterpriseKnowledgeGraph/services  |  FailureCount  |  Nombre de défaillances  |  Count  |  Count | 
| Non  | Non |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccessCount  |  Nombre de succès  |  Count  |  Count | 
| Non  | Non |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccessLatency  |  Latence de réussite  |  Millisecondes  |  Average | 
| Non  | Non |  Microsoft.EnterpriseKnowledgeGraph/services  |  TransactionCount  |  Nombre de transactions  |  Count  |  Count | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/domains  |  DeadLetteredCount  |  Événements de lettres mortes  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventGrid/domains  |  DeliveryAttemptFailCount  |  Événements d’échec de la remise  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/domains  |  DeliverySuccessCount  |  Événements remis  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventGrid/domains  |  DestinationProcessingDurationInMs  |  Durée du traitement de la destination  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/domains  |  DroppedEventCount  |  Événements annulés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/domains  |  MatchedEventCount  |  Événements correspondants  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/domains  |  PublishFailCount  |  Événements d'échec de la publication  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/domains  |  PublishSuccessCount  |  Événements publiés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/domains  |  PublishSuccessLatencyInMs  |  Latence de réussite de la publication  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Événements de lettres mortes  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Événements d’échec de la remise  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Événements remis  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Durée du traitement de la destination  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  Événements annulés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Événements correspondants  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  Événements d'échec de la publication  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  Événements publiés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Latence de réussite de la publication  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  Événements sans correspondance  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/topics  |  PublishFailCount  |  Événements d'échec de la publication  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/topics  |  PublishSuccessCount  |  Événements publiés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/topics  |  PublishSuccessLatencyInMs  |  Latence de réussite de la publication  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventGrid/topics  |  UnmatchedEventCount  |  Événements sans correspondance  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Count  |  Average | 
| Non  | Non |  Microsoft.EventHub/clusters  |  AvailableMemory  |  Mémoire disponible  |  Pourcentage  |  Maximale | 
| Non  | Non |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  Backlog des captures.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/clusters  |  CapturedBytes  |  Octets capturés.  |  Octets  |  Total | 
| Non  | Non |  Microsoft.EventHub/clusters  |  CapturedMessages  |  Messages capturés.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/clusters  |  ConnectionsClosed  |  Connexions fermées.  |  Count  |  Average | 
| Non  | Non |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Connexions ouvertes.  |  Count  |  Average | 
| Non  | Non |  Microsoft.EventHub/clusters  |  UC  |  UC  |  Pourcentage  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  Octets entrants.  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  Messages entrants  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  Demandes entrantes  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  Octets sortants.  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  Messages sortants  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  Erreurs de dépassement de quota.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/clusters  |  ServerErrors  |  Erreurs de serveur.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  Requêtes ayant réussi  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Demandes limitées.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/clusters  |  UserErrors  |  Erreurs d’utilisateur.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Average | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  Backlog des captures.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  Octets capturés.  |  Octets  |  Total | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  Messages capturés.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  ConnectionsClosed  |  Connexions fermées.  |  Count  |  Average | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Connexions ouvertes.  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  EHABL  |  Archiver les messages de backlog (déconseillé)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Archiver le débit des messages (déconseillé)  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Messages archivés (déconseillé)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Octets entrants (déconseillé)  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Octets entrants (obsolète) (déprécié)  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Messages entrants (déconseillé)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Octets sortants (déconseillé)  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Octets sortants (obsolète) (déprécié)  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Messages sortants (déconseillé)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Requêtes non réussies (déconseillé)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  Octets entrants.  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  Messages entrants  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  Demandes entrantes  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Messages entrants (obsolète) (déprécié)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  INREQS  |  Requêtes entrantes (déconseillé)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  INTERR  |  Erreurs internes du serveur (déconseillé)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Autres erreurs (déconseillé)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  Octets sortants.  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  Messages sortants  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Messages sortants (obsolète) (déprécié)  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  Erreurs de dépassement de quota.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  ServerErrors  |  Erreurs de serveur.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  Taille  |  Taille  |  Octets  |  Average | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  Requêtes ayant réussi  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Requêtes réussies (déconseillé)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Erreurs de serveur occupé (déconseillé)  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Demandes limitées.  |  Count  |  Total | 
| Non  | Non |  Microsoft.EventHub/namespaces  |  UserErrors  |  Erreurs d’utilisateur.  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  Demandes de la passerelle par catégorie  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Demandes de la passerelle  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.HDInsight/clusters  |  NumActiveWorkers  |  Nombre de collaborateurs actifs  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.HDInsight/clusters  |  ScalingRequests  |  Demandes de mise à l’échelle  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  Seuil de métrique  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  Capacité observée  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  Valeur de métrique observée  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Actions de mise à l’échelle initiées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  availabilityResults/availabilityPercentage  |  Disponibilité  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.Insights/Components  |  availabilityResults/count  |  Tests de disponibilité  |  Count  |  Count | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  availabilityResults/duration  |  Durée du test de disponibilité  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  Temps de connexion au réseau pour le chargement de page  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  Temps de traitement du client  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  Temps de réception de réponse  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  Temps d’envoi de demande  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  Temps de chargement de la page de navigateur  |  Millisecondes  |  Average | 
| Non  | Non |  Microsoft.Insights/Components  |  dependencies/count  |  Appels de dépendance  |  Count  |  Count | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  dependencies/duration  |  Durée de la dépendance  |  Millisecondes  |  Average | 
| Non  | Non |  Microsoft.Insights/Components  |  dependencies/failed  |  Échecs d'appel de dépendance  |  Count  |  Count | 
| Non  | Non |  Microsoft.Insights/Components  |  exceptions/browser  |  Exceptions du navigateur  |  Count  |  Count | 
| **Oui**  | **Oui** |  Microsoft.Insights/Components  |  exceptions/count  |  Exceptions  |  Count  |  Count | 
| Non  | Non |  Microsoft.Insights/Components  |  exceptions/server  |  Exceptions de serveur  |  Count  |  Count | 
| **Oui**  | **Oui** |  Microsoft.Insights/Components  |  pageViews/count  |  Affichages de page  |  Count  |  Count | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  pageViews/duration  |  Temps de chargement de la page consultée  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  Taux d’exceptions  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  Mémoire disponible  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  Processeur de processus  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  Taux d’E/S du processus  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  Temps processeur  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  Octets privés du processus  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  Durée d’exécution de la requête HTTP  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  Requêtes HTTP dans la file d’attente de l’application  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  Taux de requêtes HTTP  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.Insights/Components  |  requests/count  |  Requêtes serveur  |  Count  |  Count | 
| **Oui**  | Non |  Microsoft.Insights/Components  |  requests/duration  |  Temps de réponse du serveur  |  Millisecondes  |  Average | 
| Non  | Non |  Microsoft.Insights/Components  |  requests/failed  |  Demandes ayant échoué  |  Count  |  Count | 
| Non  | Non |  Microsoft.Insights/Components  |  requests/taux  |  Taux de requêtes du serveur  |  CountPerSecond  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Insights/Components  |  traces/count  |  Traces  |  Count  |  Count | 
| **Oui**  | Non |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Correspondances totales de l'API de service  |  Count  |  Count | 
| **Oui**  | Non |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  Latence globale de l'API de service  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Résultats totaux de l'API de service  |  Count  |  Count | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  CacheUtilization  |  Utilisation du cache  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  ContinuousExportMaxLatenessMinutes  |  Minutes de retard max. pour l’exportation continue  |  Count  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  ContinuousExportNumOfRecordsExported  |  Exportation continue : nombre d’enregistrements exportés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  ContinuousExportPendingCount  |  Nombre en attente d’exportations continues  |  Count  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  ContinuousExportResult  |  Résultat de l’exportation continue  |  Count  |  Count | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  UC  |  UC  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  EventsProcessedForEventHubs  |  Événements traités (pour Event/IoT Hubs)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  ExportUtilization  |  Utilisation de l’exportation  |  Pourcentage  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  IngestionLatencyInSeconds  |  Latence d’ingestion (en secondes)  |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Résultat de l’ingestion  |  Count  |  Count | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  IngestionUtilization  |  Utilisation de l’ingestion  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  IngestionVolumeInMB  |  Volume d’ingestion (en Mo)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  KeepAlive  |  Keep alive  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  Durée de la requête  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  SteamingIngestRequestRate  |  Taux de demandes d’ingestion en streaming  |  Count  |  RateRequestsPerSecond | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  StreamingIngestDataRate  |  Taux de données d’ingestion en streaming  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  StreamingIngestDuration  |  Durée de l’ingestion en streaming  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Kusto/Clusters  |  StreamingIngestResults  |  Résultat de l’ingestion en streaming  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency  |  Latence de l’action   |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Actions terminées   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsFailed  |  Actions ayant échoué   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Actions ignorées   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsStarted  |  Actions démarrées   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Actions ayant réussi   |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Latence de réussite d’action   |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Événements d’action limitée  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Utilisation de la mémoire du connecteur pour l’environnement de service d’intégration  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Utilisation du processeur du connecteur pour l’environnement de service d’intégration  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Utilisation de la mémoire de flux de travail pour l’environnement de service d’intégration  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Utilisation du processeur de flux de travail pour l’environnement de service d’intégration  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Pourcentage d’échec des exécutions  |  Pourcentage  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency  |  Latence d’exécution  |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Exécutions annulées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted  |  Exécutions terminées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  Exécutions ayant échoué  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  Exécutions démarrées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Exécutions réussies  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Événements limités démarrés par l’exécution  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Latence d’exécution ayant réussi  |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Exécuter événements limités  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Latence d’activation de déclencheur   |  Secondes  |  Average | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency  |  Latence de déclencheur   |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Déclenchements terminés   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFailed  |  Déclenchements ayant échoué   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired  |  Déclenchements activés   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Déclenchements ignorés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersStarted  |  Déclenchements lancés   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Déclenchements ayant réussi   |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Latence déclencheur ayant réussi   |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Événements limités par déclencheur  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/workflows  |  ActionLatency  |  Latence de l’action   |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  Actions terminées   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  ActionsFailed  |  Actions ayant échoué   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  Actions ignorées   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  ActionsStarted  |  Actions démarrées   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  Actions ayant réussi   |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Latence de réussite d’action   |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  Événements d’action limitée  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  Exécutions d’actions facturables  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  Exécutions de déclencheurs facturables  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Utilisation de la facturation pour les exécutions d’opérations natives  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Utilisation de la facturation pour les exécutions d’opérations natives  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Utilisation de la facturation pour les exécutions de connecteurs Standard  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Utilisation de la facturation pour les exécutions de connecteurs Standard  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Utilisation de la facturation pour les exécutions de consommation du stockage  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Utilisation de la facturation pour les exécutions de consommation du stockage  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  Pourcentage d’échec des exécutions  |  Pourcentage  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/workflows  |  RunLatency  |  Latence d’exécution  |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Exécutions annulées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Exécutions terminées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  RunsFailed  |  Exécutions ayant échoué  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  RunsStarted  |  Exécutions démarrées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  Exécutions réussies  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  RunStartThrottledEvents  |  Événements limités démarrés par l’exécution  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Latence d’exécution ayant réussi  |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  Exécuter événements limités  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  Nombre total d’exécutions facturables  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Latence d’activation de déclencheur   |  Secondes  |  Average | 
| **Oui**  | Non |  Microsoft.Logic/workflows  |  TriggerLatency  |  Latence de déclencheur   |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  Déclenchements terminés   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  TriggersFailed  |  Déclenchements ayant échoué   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  TriggersFired  |  Déclenchements activés   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  Déclenchements ignorés  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  TriggersStarted  |  Déclenchements lancés   |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  Déclenchements ayant réussi   |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Latence déclencheur ayant réussi   |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  Événements limités par déclencheur  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Cœurs actifs  |  Cœurs actifs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Nœuds actifs  |  Nœuds actifs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Exécutions terminées  |  Exécutions terminées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Exécutions échouées  |  Exécutions échouées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Cœurs inactifs  |  Cœurs inactifs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Nœuds inactifs  |  Nœuds inactifs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Cœurs sortants  |  Cœurs sortants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Nœuds sortants  |  Nœuds sortants  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Échec du déploiement de modèle  |  Échec du déploiement de modèle  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Déploiements de modèles commencés  |  Déploiements de modèles commencés  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Déploiements de modèles réussis  |  Déploiements de modèles réussis  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Enregistrements de modèles ayant échoué  |  Enregistrements de modèles ayant échoué  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Enregistrements de modèles réussis  |  Enregistrements de modèles réussis  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Cœurs réquisitionnés  |  Cœurs réquisitionnés  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Nœuds reportés  |  Nœuds reportés  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Pourcentage d’utilisation du quota  |  Pourcentage d’utilisation du quota  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Exécutions démarrées  |  Exécutions démarrées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Nombre total de cœurs  |  Nombre total de cœurs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Nombre total de nœuds  |  Nombre total de nœuds  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Cœurs inutilisables  |  Cœurs inutilisables  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.MachineLearningServices/workspaces  |  Nœuds inutilisables  |  Nœuds inutilisables  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Maps/accounts  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.Maps/accounts  |  Usage  |  Usage  |  Count  |  Count | 
| **Oui**  | Non |  Microsoft.Media/mediaservices  |  AssetCount  |  Nombre de ressources  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Media/mediaservices  |  AssetQuota  |  Quota de ressources  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Media/mediaservices  |  AssetQuotaUsedPercentage  |  Pourcentage du quota de ressources utilisé  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Media/mediaservices  |  ContentKeyPolicyCount  |  Nombre de stratégies de clé de contenu  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuota  |  Quota de stratégies de clé de contenu  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuotaUsedPercentage  |  Pourcentage du quota de stratégies de clé de contenu utilisé  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Media/mediaservices  |  StreamingPolicyCount  |  Nombre de stratégies de diffusion en continu  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota  |  Quota de stratégies de diffusion en continu  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  Pourcentage du quota de stratégies de diffusion en continu utilisé  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Media/mediaservices/streamingEndpoints  |  Sortie  |  Sortie  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Media/mediaservices/streamingEndpoints  |  Demandes  |  Demandes  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Media/mediaservices/streamingEndpoints  |  SuccessE2ELatency  |  Latence de réussite de bout en bout  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Nombre d’interruptions du GC  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Durée totale de pause du GC  |  Millisecondes  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Taille maximale des données d’ancienne génération disponibles  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Taille des données d’ancienne génération  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Promouvoir à la taille des données d’ancienne génération  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Pourcentage d’utilisation du processeur de service  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Mémoire de service affectée  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Mémoire de service maximale  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Mémoire de service utilisée  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Pourcentage d’utilisation du processeur système  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatErrorCount  |  Erreur globale Tomcat  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Nombre total d’octets reçus par Tomcat  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Durée maximale des demandes Tomcat  |  Millisecondes  |  Maximale | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Nombre total de demandes Tomcat  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Durée totale des demandes Tomcat  |  Millisecondes  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Durée moyenne des demandes Tomcat  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatSentBytes  |  Nombre total d’octets envoyés par Tomcat  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Nombre de sessions actives Tomcat  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Nombre maximal de sessions actives Tomcat  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Durée maximale des sessions actives Tomcat  |  Millisecondes  |  Maximale | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Nombre de sessions Tomcat créées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Nombre de sessions Tomcat ayant expiré  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Nombre de sessions Tomcat rejetées  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Promouvoir à la taille des données de nouvelle génération  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Taille allouée utilisée du pool de volumes  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Taille logique totale du pool de volumes  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageReadLatency  |  Latence de lecture moyenne  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageWriteLatency  |  Latence d’écriture moyenne  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops  |  E/S par seconde en lecture  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogicalSize  |  Taille logique du volume  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  Taille d’instantané du volume  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops  |  E/S par seconde en écriture  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Durée totale d’Application Gateway  |  Millisecondes  |  Average | 
| Non  | Non |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Demandes par minute par hôte sain  |  Count  |  Average | 
| Non  | Non |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Temps de connexion au back-end  |  Millisecondes  |  Average | 
| Non  | Non |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Temps de réponse du premier octet du back-end  |  Millisecondes  |  Average | 
| Non  | Non |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Temps de réponse du dernier octet du back-end  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  État de la réponse du back-end  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  BlockedCount  |  Distribution des règles de demandes bloquées du pare-feu d’applications web  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Nombre de demandes bloquées du pare-feu d’applications web  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Octets reçus  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  BytesSent  |  Octets envoyés  |  Octets  |  Total | 
| Non  | Non |  Microsoft.Network/applicationGateways  |  CapacityUnits  |  Unités de capacité actuelles  |  Count  |  Average | 
| Non  | Non |  Microsoft.Network/applicationGateways  |  ClientRtt  |  RTT client  |  Millisecondes  |  Average | 
| Non  | Non |  Microsoft.Network/applicationGateways  |  ComputeUnits  |  Unités Compute actuelles  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Connexions courantes  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  FailedRequests  |  Demandes ayant échoué  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Nombre d’hôtes intègres  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Distribution totale des règles du pare-feu d’applications web  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  État de la réponse  |  Count  |  Total | 
| Non  | Non |  Microsoft.Network/applicationGateways  |  Débit  |  Débit  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  TlsProtocol  |  Protocole TLS du client  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Total de requêtes  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Nombre d’hôtes non intègres  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit  |  Nombre d'accès aux règles d'application  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/azurefirewalls  |  DataProcessed  |  Données traitées  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  État d’intégrité du pare-feu  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Nombre d’accès aux règles de réseau  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/azurefirewalls  |  SNATPortUtilization  |  Utilisation des ports SNAT  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Network/connections  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/dnszones  |  QueryVolume  |  Volume de requête  |  Count  |  Total | 
| Non  | Non |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Utilisation de la capacité du jeu d’enregistrements  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/dnszones  |  RecordSetCount  |  Nombre de jeux d’enregistrements  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  Disponibilité du protocole ARP  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Network/expressRouteCircuits  |  BgpAvailability  |  Disponibilité du protocole BGP  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| Non  | Non |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/expressRoutePorts  |  AdminState  |  AdminState  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Pourcentage d’intégrité du backend  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Nombre de requêtes de backend  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  Latence de requête du backend  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Network/frontdoors  |  BillableResponseSize  |  Taille de la réponse facturable  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Network/frontdoors  |  RequestCount  |  Nombre de requêtes  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Network/frontdoors  |  RequestSize  |  Taille de la requête  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Network/frontdoors  |  ResponseSize  |  Taille de la réponse  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Network/frontdoors  |  TotalLatency  |  Latence totale  |  Millisecondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Nombre de requêtes du pare-feu d’applications web  |  Count  |  Total | 
| Non  | Non |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  Ports SNAT alloués (Préversion)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/loadBalancers  |  ByteCount  |  Nombre d’octets  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/loadBalancers  |  DipAvailability  |  État de la sonde d’intégrité  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Network/loadBalancers  |  PacketCount  |  Nombre de paquets  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Nombre de connexions SNAT  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/loadBalancers  |  SYNCount  |  Nombre de SYN  |  Count  |  Total | 
| Non  | Non |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Ports SNAT utilisés (Préversion)  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Disponibilité du chemin d’accès aux données  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Octets reçus  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Octets envoyés  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Paquets reçus  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Paquets envoyés  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Avg. Durée aller-retour (ms)  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Pourcentage de vérifications ayant échoué (préversion)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  % de sondes ayant échoué  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Durée aller-retour (ms) (préversion)  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Nombre d’octets  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  Octets entrants supprimés DDoS  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Octets entrants transférés DDoS  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  Octets entrants DDoS  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Paquets SYN entrants pour déclencher l’atténuation DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Paquets TCP entrants pour déclencher l’atténuation DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Paquets UDP entrants pour déclencher l’atténuation DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  Sous attaque DDoS ou non  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  Nombre de paquets  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  Paquets entrants ignorés DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  Paquets entrants transférés DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  Paquets entrants DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  SynCount  |  Nombre de SYN  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  Octets TCP entrants supprimés DDoS  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Octets TCP entrants transférés DDoS  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  Octets TCP entrants DDoS  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  Paquets TCP entrants TCP ignorés DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Paquets TCP entrants transférés DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  Paquets TCP entrants DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Octets UDP entrants supprimés DDoS  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Octets UDP entrants transférés DDoS  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  Octets UDP entrants DDoS  |  BytesPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Paquets UDP entrants ignorés DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Paquets UDP entrants transférés DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  Paquets UDP entrants DDoS  |  CountPerSecond  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  Disponibilité du chemin d’accès aux données  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  État du point de terminaison par point de terminaison  |  Count  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Requêtes par point de terminaison renvoyé  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  Bande passante S2S de passerelle  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Bande passante P2S de passerelle  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  Nombre de connexions P2S  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Bande passante de tunnel  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Octets de sortie de tunnel  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Rejet de paquets TS de sortie de tunnel pour incompatibilité  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Paquets de sortie de tunnel  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Octets d’entrée de tunnel  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Rejet de paquets TS d’entrée de tunnel pour incompatibilité  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Paquets en entrée de tunnel  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Durée aller-retour pour les commandes ping à destination d’une machine virtuelle  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Commandes ping à destination d’une machine virtuelle qui ont échoué  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming  |  Messages entrants  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.failedrequests  |  Toutes les requêtes entrantes ayant échoué  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  Toutes les demandes entrantes  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  Notifications Push planifiées envoyées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  Notifications Push planifiées annulées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.all  |  Opérations de gestion de l’installation  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Opérations de suppression d’installation  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  Opérations d’obtention d’installation  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  Opérations d’installation de correctif  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  Opérations de création ou de mise à jour d’installation  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  Toutes les notifications sortantes  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Erreurs de canal incorrect ou arrivé à expiration  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  Erreurs de canal  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Erreurs de charge utile  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  Erreurs système de notification externe  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Notifications réussies  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  Erreur de canal APNS incorrect  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  Erreur de canal APNS arrivé à expiration  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  Erreurs d’autorisation APNS  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Erreur de taille de notification APNS non valide  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  Erreurs APNS  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  Notifications APNS réussies  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationerror  |  Erreurs d’authentification GCM  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  Erreur de canal GCM incorrect  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  Erreur de canal GCM arrivé à expiration  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  Erreurs d’autorisation GCM (informations d’identification non valides)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  Format de notification GCM non valide  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  Erreur de taille de notification GCM non valide  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  Erreurs GCM  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  Notifications GCM réussies  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  Notifications GCM limitées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  Erreur de canal GCM incorrect  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  Erreurs d’authentification MPNS  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  Erreur de canal incorrect MPNS  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  Canal MPNS déconnecté  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  Notifications MPNS supprimées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  Informations d’identification MPNS non valides  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  Format de notification MPNS non valide  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  Erreurs MPNS  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  Notifications MPNS réussies  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  Notifications MPNS limitées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  Erreurs d’authentification WNS  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  Erreur de canal WNS incorrect  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  Canal WNS déconnecté  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  Canal WNS limité  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  Notifications WNS supprimées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  Erreur de canal WNS arrivé à expiration  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  Erreurs d’autorisation WNS (informations d’identification non valides)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Format de notification WNS non valide  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  Erreur de taille de notification WNS non valide  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  Erreurs d’autorisation WNS (jeton non valide)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  Erreurs WNS  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  Notifications WNS réussies  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  Notifications WNS limitées  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  Erreurs d’autorisation WNS (inaccessible)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  Erreurs d’autorisation WNS (jeton incorrect)  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.all  |  Opérations d’inscription  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  Opérations de création d’inscription  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  Opérations de suppression d’inscription  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  Opérations de lecture d’inscription  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  Opérations de mise à jour d’inscription  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  scheduled.pending  |  Notifications planifiées en attente  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Memory  |  % Available Memory  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Swap Space  |  % Available Swap Space  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Octets validés en cours d’utilisation Average_%  |  % d’octets validés utilisés  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% DPC Time  |  % DPC Time  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Inodes libres Average_%  |  % Free Inodes  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % Free Space  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % Free Space  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% Idle Time  |  % Idle Time  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% Interrupt Time  |  % Interrupt Time  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% IO Wait Time  |  % IO Wait Time  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% Nice Time  |  % Nice Time  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% Privileged Time  |  % Privileged Time  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Temps processeur Average_%  |  % temps processeur  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Temps processeur Average_%  |  % temps processeur  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Inodes utilisés Average_%  |  % Used Inodes  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Memory  |  % Used Memory  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Inodes utilisés Average_%  |  % Used Space  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Swap Space  |  % Used Swap Space  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_% User Time  |  % User Time  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes  |  Nombre d’octets disponibles  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Memory  |  Available MBytes Memory  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Swap  |  Available MBytes Swap  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Read  |  Avg. Disk sec/Read  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Read  |  Avg. Disk sec/Read  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Transfer  |  Avg. Disk sec/Transfer  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Write  |  Avg. Disk sec/Write  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Disk sec/Write  |  Avg. Disk sec/Write  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Received/sec  |  Octets reçus/s  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Sent/sec  |  Octets envoyés/s  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/sec  |  Nombre total d’octets/s  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Longueur de file d’attente du disque Average_Current  |  Longueur actuelle de la file d'attente du disque  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Octets de lecture/s Average_Disk  |  Nb d’octets de lecture de disque/s  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Nb d’opérations de lectures de disque/s  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Nb d’opérations de lectures de disque/s  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Disk Transfers/sec  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Disk Transfers/sec  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Octets d’écriture/s Average_Disk  |  Nb d’octets d’écriture de disque/s  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Nb d’opération d’écriture de disque/s  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Nb d’opération d’écriture de disque/s  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Mégaoctets Average_Free  |  Free Megabytes  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Mégaoctets Average_Free  |  Free Megabytes  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Free Physical Memory  |  Free Physical Memory  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Free Space in Paging Files  |  Free Space in Paging Files  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Free Virtual Memory  |  Free Virtual Memory  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Logical Disk Bytes/sec  |  Logical Disk Bytes/sec  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Page Reads/sec  |  Page Reads/sec  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Page Writes/sec  |  Page Writes/sec  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec  |  Pages/sec  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Pct Privileged Time  |  Pct Privileged Time  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Pct User Time  |  Pct User Time  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Physical Disk Bytes/sec  |  Physical Disk Bytes/sec  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  Processus  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Longueur de la file d’attente Average_Processor  |  Longueur de la file d’attente du processeur  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Size Stored In Paging Files  |  Size Stored In Paging Files  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes  |  Total Bytes  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Received  |  Total Bytes Received  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Transmitted  |  Total Bytes Transmitted  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Total Collisions  |  Total Collisions  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Received  |  Total Packets Received  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Transmitted  |  Total Packets Transmitted  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Total Rx Errors  |  Total Rx Errors  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Total Rx Errors  |  Total Tx Errors  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Uptime  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Used MBytes Swap Space  |  Used MBytes Swap Space  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory MBytes  |  Used Memory kBytes  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory MBytes  |  Used Memory MBytes  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  Utilisateurs  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual Shared Memory  |  Virtual Shared Memory  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Événement  |  Événement  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.OperationalInsights/workspaces  |  Heartbeat  |  Heartbeat  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.OperationalInsights/workspaces  |  Update  |  Update  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  Mémoire  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  Vidage de mémoire (jeux de données)  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  Utilisation élevée du QPU  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  Durée de la requête (jeux de données)  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  Longueur de la file d’attente des travaux du pool de requêtes (jeux de données)  |  Count  |  Average | 
| Non  | Non |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Relay/namespaces  |  BytesTransferred  |  BytesTransferred  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Count  |  Total | 
| Non  | Non |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Search/searchServices  |  SearchLatency  |  Latence de recherche  |  Secondes  |  Average | 
| **Oui**  | Non |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Requêtes de recherche par seconde  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Pourcentage de requêtes de recherche limitées  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Total | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Nombre de messages actifs dans une file d’attente/rubrique.  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  ConnectionsClosed  |  Connexions fermées.  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Connexions ouvertes.  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  Processeur (déprécié)  |  Pourcentage  |  Maximale | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  DeadletteredMessages  |  Nombre de messages de lettres mortes dans une file d’attente/rubrique.  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  Messages entrants  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Demandes entrantes  |  Count  |  Total | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  Messages  |  Nombre de messages dans une file d’attente/rubrique.  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUsage  |  UC  |  Pourcentage  |  Maximale | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  NamespaceMemoryUsage  |  Utilisation de la mémoire  |  Pourcentage  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  Messages sortants  |  Count  |  Total | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  ScheduledMessages  |  Nombre de messages planifiés dans une file d’attente/rubrique.  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Erreurs de serveur.  |  Count  |  Total | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  Taille  |  Taille  |  Octets  |  Average | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  Requêtes ayant réussi  |  Count  |  Total | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Demandes limitées.  |  Count  |  Total | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Erreurs d’utilisateur.  |  Count  |  Total | 
| Non  | Non |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Utilisation de la mémoire (déprécié)  |  Pourcentage  |  Maximale | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  ActualCpu  |  ActualCpu  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  ActualMemory  |  ActualMemory  |  Octets  |  Average | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  AllocatedCpu  |  AllocatedCpu  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  AllocatedMemory  |  AllocatedMemory  |  Octets  |  Average | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  ApplicationStatus  |  ApplicationStatus  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  ContainerStatus  |  ContainerStatus  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  CpuUtilization  |  CpuUtilization  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  MemoryUtilization  |  MemoryUtilization  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  RestartCount  |  RestartCount  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Count  |  Average | 
| Non  | Non |  Microsoft.ServiceFabricMesh/applications  |  ServiceStatus  |  ServiceStatus  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Nombre de connexions  |  Count  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  Trafic entrant  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  Nombre de messages  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  Trafic sortant  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  Erreurs système  |  Pourcentage  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  Erreurs d’utilisateur  |  Pourcentage  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Pourcentage d’UC moyenne  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  Octets d’E/S lus  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  Octets d’E/S écrits  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/managedInstances  |  io_requests  |  Nombre de requêtes d’E/S  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Espace de stockage réservé  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Espace de stockage utilisé  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Nombre de cœurs virtuels  |  Count  |  Average | 
| Non  | Non |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  Pourcentage DTU  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.Sql/servers  |  database_storage_used  |  Espace de données utilisé  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  Pourcentage DTU  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers  |  dtu_used  |  DTU utilisé  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers  |  storage_used  |  Espace de données utilisé  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Espace de données alloué  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  Processeur d'application facturé  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  Pourcentage processeur d'application  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Pourcentage de mémoire de l’application  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Bloqué par le pare-feu  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Pourcentage d’accès au cache  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Pourcentage de cache utilisé  |  Pourcentage  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Connexions ayant échoué  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Connexions réussies  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  cpu_limit  |  Limite UC  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  Pourcentage UC  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  cpu_used  |  UC utilisée  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  deadlock  |  Blocages  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  Pourcentage DTU  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  dtu_limit  |  Limite DTU  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  dtu_used  |  DTU utilisé  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  Pourcentage DWU  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  dwu_limit  |  Limite DWU  |  Count  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  dwu_used  |  DWU utilisé  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Pourcentage de tempdb locale  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Pourcentage E/S du journal  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Pourcentage de mémoire  |  Pourcentage  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Pourcentage E/S des données  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Pourcentage de sessions  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  storage  |  Espace de données utilisé  |  Octets  |  Maximale | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  storage_percent  |  Pourcentage d'espace de données utilisé  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Taille du fichier de données tempdb en kilo-octets  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Taille du fichier journal de tempdb en kilo-octets  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Pourcentage d’utilisation du journal tempdb  |  Pourcentage  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Pourcentage de travaux  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Pourcentage de stockage OLTP en mémoire  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Espace de données alloué  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Pourcentage d'espace de données alloué  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  Limite UC  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  Pourcentage UC  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  UC utilisée  |  Count  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Espace de données alloué  |  Octets  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  Limite UC  |  Count  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  Pourcentage UC  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  UC utilisée  |  Count  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  Pourcentage DTU  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU utilisé  |  Count  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Pourcentage E/S du journal  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Pourcentage E/S des données  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Pourcentage de sessions  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Espace de données utilisé  |  Octets  |  Average | 
| Non  | Non |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Pourcentage de travaux  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  Pourcentage DTU  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  Limite eDTU  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU utilisé  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Pourcentage E/S du journal  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Pourcentage E/S des données  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Pourcentage de sessions  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Taille maximale des données  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Pourcentage d'espace de données utilisé  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Espace de données utilisé  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Taille du fichier de données tempdb en kilo-octets  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Taille du fichier journal de tempdb en kilo-octets  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Pourcentage d’utilisation du journal tempdb  |  Pourcentage  |  Maximale | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Pourcentage de travaux  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Pourcentage de stockage OLTP en mémoire  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts  |  Sortie  |  Sortie  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts  |  Entrée  |  Entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Latence E2E de réussite  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Latence du serveur avec requête réussie  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts  |  Transactions  |  Transactions  |  Count  |  Total | 
| Non  | Non |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Capacité utilisée  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/blobServices  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| Non  | Non |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Capacité d’objet blob  |  Octets  |  Average | 
| Non  | Non |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Nombre d’objets blob  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Nombre de conteneurs d’objets blob  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/blobServices  |  Sortie  |  Sortie  |  Octets  |  Total | 
| Non  | Non |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Capacité d'index  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/blobServices  |  Entrée  |  Entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Latence E2E de réussite  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Latence du serveur avec requête réussie  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/blobServices  |  Transactions  |  Transactions  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  Sortie  |  Sortie  |  Octets  |  Total | 
| Non  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  Capacité de fichiers  |  Octets  |  Average | 
| Non  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Nombre de fichiers  |  Count  |  Average | 
| Non  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Nombre de partages de fichiers  |  Count  |  Average | 
| Non  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareQuota  |  Taille du quota de partage de fichiers  |  Octets  |  Average | 
| Non  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Nombre d’instantanés de partage de fichiers  |  Count  |  Average | 
| Non  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Taille d’instantané de partage de fichiers  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  Entrée  |  Entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Latence E2E de réussite  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Latence du serveur avec requête réussie  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/fileServices  |  Transactions  |  Transactions  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/queueServices  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/queueServices  |  Sortie  |  Sortie  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/queueServices  |  Entrée  |  Entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Capacité de la file d’attente  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  Nombre de files d’attente  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Nombre de messages dans la file d’attente  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Latence E2E de réussite  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Latence du serveur avec requête réussie  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/queueServices  |  Transactions  |  Transactions  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/tableServices  |  Disponibilité  |  Disponibilité  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/tableServices  |  Sortie  |  Sortie  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/tableServices  |  Entrée  |  Entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Latence E2E de réussite  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Latence du serveur avec requête réussie  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  Capacité de la table  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  Nombre de tables  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Nombre d’entités de table  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.Storage/storageAccounts/tableServices  |  Transactions  |  Transactions  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  ClientIOPS  |  Total des IOPS du client  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  ClientLatency  |  Latence moyenne du client  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  ClientLockIOPS  |  IOPS de verrouillage du client  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  ClientMetadataReadIOPS  |  IOPS de lecture de métadonnées client  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  ClientMetadataWriteIOPS  |  IOPS d’écriture de métadonnées client  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  ClientReadIOPS  |  IOPS de lecture du client  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  ClientReadThroughput  |  Débit moyen de lecture du cache  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  ClientWriteIOPS  |  IOPS d’écriture du client  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  ClientWriteThroughput  |  Débit moyen d’écriture dans le cache  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetAsyncWriteThroughput  |  Débit d’écriture asynchrone dans StorageTarget  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetFillThroughput  |  Débit de remplissage de StorageTarget  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetHealth  |  Intégrité de la cible de stockage  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetIOPS  |  Total des IOPS dans StorageTarget  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetLatency  |  Latence de StorageTarget  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetMetadataReadIOPS  |  IOPS de lecture des métadonnées dans StorageTarget  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetMetadataWriteIOPS  |  IOPS d’écriture des métadonnées dans StorageTarget  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetReadAheadThroughput  |  Débit de lecture anticipée dans StorageTarget  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetReadIOPS  |  IOPS de lecture dans StorageTarget  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  Débit d’écriture synchrone dans StorageTarget  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetTotalReadThroughput  |  Débit total de lecture dans StorageTarget  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  Débit total d’écriture dans StorageTarget  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  StorageTargetWriteIOPS  |  IOPS d’écriture dans StorageTarget  |  Count  |  Average | 
| **Oui**  | Non |  Microsoft.StorageCache/caches  |  Uptime  |  Uptime  |  Count  |  Average | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Résultat de session de synchronisation  |  Count  |  Average | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Octets synchronisés  |  Octets  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Taille de rappel de hiérarchisation cloud par application  |  Octets  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Taille de rappel de la hiérarchisation cloud  |  Octets  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Rappel de hiérarchisation cloud  |  Octets  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Débit de rappel de la hiérarchisation cloud  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  État du serveur en ligne  |  Count  |  Maximale | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Fichiers synchronisés  |  Count  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Fichiers ne se synchronisant pas  |  Count  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat  |  État du serveur en ligne  |  Count  |  Maximale | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Rappel de hiérarchisation cloud  |  Octets  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Octets synchronisés  |  Octets  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Fichiers synchronisés  |  Count  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Fichiers ne se synchronisant pas  |  Count  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Octets synchronisés  |  Octets  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Fichiers synchronisés  |  Count  |  Total | 
| **Oui**  | Non |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Fichiers ne se synchronisant pas  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Requêtes de fonction ayant échoué  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Événements de fonction  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Requêtes de fonction  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  Erreurs de conversion de données  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  Erreurs de désérialisation d’entrée  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Événements en désordre  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Événements d’entrée précoces  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  Erreurs  |  Erreurs d’exécution  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Octets des événements d’entrée  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Événements d’entrée  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Événements d'entrée en backlog  |  Count  |  Maximale | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Sources d'entrée reçues  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  Événements d’entrée tardifs  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Événements de sortie  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Délai en filigrane  |  Secondes  |  Maximale | 
| **Oui**  | Non |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  Utilisation de % d’unités de diffusion  |  Pourcentage  |  Maximale | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Read Bytes  |  Disk Read Bytes  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Read Operations/Sec  |  Disk Read Operations/Sec  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Write Bytes  |  Disk Write Bytes  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  Disk Write Operations/Sec  |  Disk Write Operations/Sec  |  CountPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Disk Read Bytes/Sec  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Latence de lecture sur disque  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Opérations de lecture sur disque  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Disk Write Bytes/Sec  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Latence d'écriture sur disque  |  Millisecondes  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Opérations d'écriture sur disque  |  Count  |  Total | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Mémoire active  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  Mémoire allouée  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  Mémoire utilisée  |  Octets  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  Network In  |  Network In  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  Network Out  |  Network Out  |  Octets  |  Total | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Octets entrants réseau/s  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Octets sortants réseau/s  |  BytesPerSecond  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Pourcentage  |  Average | 
| **Oui**  | Non |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  Pourcentage de processeurs prêts  |  Millisecondes  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Requêtes actives  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Temps de réponse moyen  |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Données entrantes  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Données sortantes  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Pourcentage UC  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Longueur de file d'attente de disque  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  HTTP 101  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http 2xx  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http 3xx  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  Http 401  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  Http 403  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http 4xx  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Erreurs de serveur http  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Longueur de la file d’attente HTTP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Workers de plan App Service de grande taille  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Workers de plan App Service de taille moyenne  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Pourcentage de mémoire  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Demandes  |  Demandes  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Workers de plan App Service de petite taille  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Nombre total de serveurs frontaux  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Pourcentage UC  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Pourcentage de mémoire  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Workers disponibles  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersTotal  |  Nombre total de workers  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Workers utilisés  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  BytesReceived  |  Données entrantes  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  BytesSent  |  Données sortantes  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  Pourcentage UC  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Longueur de file d'attente de disque  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Longueur de la file d’attente HTTP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  Pourcentage de mémoire  |  Pourcentage  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  Close Wait - TCP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  TcpClosing  |  Closing - TCP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  TcpEstablished  |  Established - TCP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  Fin Wait 1 - TCP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  Fin Wait 2 - TCP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  Last Ack - TCP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  Syn Received - TCP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  Syn Sent - TCP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  Time Wait - TCP  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  AppConnections  |  Connexions  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  Plage de travail moyenne de la mémoire  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  AverageResponseTime  |  Temps de réponse moyen  |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  BytesReceived  |  Données entrantes  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  BytesSent  |  Données sortantes  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  CpuTime  |  Temps processeur  |  Secondes  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  CurrentAssemblies  |  Assemblys actuels  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  Nombre d’exécutions de fonctions  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  Unités d’exécution de fonctions  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Gen0Collections  |  Garbage collections de génération 0  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Gen1Collections  |  Garbage collections de génération 1  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Gen2Collections  |  Garbage collections de génération 2  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Poignées  |  Nombre de descripteurs  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  HealthCheckStatus  |  État de contrôle d’intégrité  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Http101  |  HTTP 101  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Http2xx  |  Http 2xx  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Http3xx  |  Http 3xx  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Http401  |  Http 401  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Http403  |  Http 403  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Http404  |  Http 404  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Http4xx  |  Http 4xx  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Http5xx  |  Erreurs de serveur http  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  HttpResponseTime  |  Temps de réponse  |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  Autres octets par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  Autres opérations par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  Octets lus par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  Opérations de lecture par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  Octets écrits par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  Opérations d’écriture par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  Plage de travail de la mémoire  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  PrivateBytes  |  Octets privés  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Demandes  |  Demandes  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  Demandes dans la file d’attente d’application  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  Threads  |  Nombre de threads  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  TotalAppDomains  |  Total des domaines d’application  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  Total des domaines d’application déchargés  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  AppConnections  |  Connexions  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  Plage de travail moyenne de la mémoire  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  Temps de réponse moyen  |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  BytesReceived  |  Données entrantes  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  BytesSent  |  Données sortantes  |  Octets  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  CpuTime  |  Temps processeur  |  Secondes  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  Assemblys actuels  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  Nombre d’exécutions de fonctions  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  Unités d’exécution de fonctions  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Garbage collections de génération 0  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Garbage collections de génération 1  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Garbage collections de génération 2  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Poignées  |  Nombre de descripteurs  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  HealthCheckStatus  |  État de contrôle d’intégrité  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Http101  |  HTTP 101  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Http2xx  |  Http 2xx  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Http3xx  |  Http 3xx  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Http401  |  Http 401  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Http403  |  Http 403  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Http4xx  |  Http 4xx  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Http5xx  |  Erreurs de serveur http  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  Temps de réponse  |  Secondes  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  Autres octets par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  Autres opérations par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  Octets lus par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  Opérations de lecture par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  Octets écrits par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  Opérations d’écriture par seconde (E/S)  |  BytesPerSecond  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  Plage de travail de la mémoire  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Octets privés  |  Octets  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Demandes  |  Demandes  |  Count  |  Total | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  Demandes dans la file d’attente d’application  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  Threads  |  Nombre de threads  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  Total des domaines d’application  |  Count  |  Average | 
| **Oui**  | **Oui** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  Total des domaines d’application déchargés  |  Count  |  Average | 
