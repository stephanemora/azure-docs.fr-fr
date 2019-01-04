---
title: Métriques prises en charge par Azure Monitor par type de ressource
description: Liste des métriques disponibles pour chaque type de ressource avec Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 09/14/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 1e81f165c3ab38fffae7523efc6cb2ae307a1a25
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53549630"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métriques prises en charge avec Azure Monitor
Azure Monitor offre plusieurs moyens d’interagir avec les métriques, y compris en créant des graphiques dans le portail, en y accédant via l’API REST ou en envoyant des requêtes avec PowerShell ou l’interface CLI. Voici une liste complète de toutes les métriques actuellement offertes par le pipeline de métrique d’Azure Monitor. D’autres métriques peuvent être disponibles dans le portail ou via les API héritées. La liste ci-dessous englobe uniquement les métriques disponibles en utilisant le pipeline de métriques Azure Monitor consolidé. Pour rechercher ces métriques et y accéder, veuillez utiliser [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions).

> [!NOTE]
> L’envoi de métriques à plusieurs dimensions via les paramètres de diagnostic n’est actuellement pas pris en charge. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
>
> *Par exemple* : La métrique « Messages entrants » sur un Event Hub peut être examinée et représentée sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|qpu_metric|QPU|Nombre|Moyenne|QPU. Plage de 0 à 100 pour S1, de 0 à 200 pour S2 et de 0 à 400 pour S4|ServerResourceType|
|memory_metric|Mémoire|Octets|Moyenne|Mémoire. Plage de 0 à 25 Go pour S1, de 0 à 50 Go pour S2 et de 0 à 100 Go pour S4|ServerResourceType|
|TotalConnectionRequests|Nombre total de demandes de connexion|Nombre|Moyenne|Nombre total de demandes de connexion. Il s’agit des arrivées.|ServerResourceType|
|SuccessfullConnectionsPerSec|Connexions réussies par seconde|CountPerSecond|Moyenne|Taux de connexions terminées réussies.|ServerResourceType|
|TotalConnectionFailures|Nombre total d’échecs de connexion|Nombre|Moyenne|Total des échecs de tentatives de connexion.|ServerResourceType|
|CurrentUserSessions|Sessions utilisateur actuelles|Nombre|Moyenne|Nombre actuel de sessions utilisateur établies.|ServerResourceType|
|QueryPoolBusyThreads|Threads occupés du pool de threads de requêtes|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads de requêtes.|ServerResourceType|
|CommandPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de commandes|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de commandes.|ServerResourceType|
|ProcessingPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de traitement|Nombre|Moyenne|Nombre de travaux autres que d’E/S contenus dans la file d’attente du pool de threads de traitement.|ServerResourceType|
|CurrentConnections|Connexion : Connexions en cours|Nombre|Moyenne|Nombre actuel de connexions client établies.|ServerResourceType|
|CleanerCurrentPrice|Mémoire : prix actuel du nettoyage|Nombre|Moyenne|Prix actuel de la mémoire, $/octet/temps, normalisé à 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Mémoire : mémoire de nettoyage réductible|Octets|Moyenne|Quantité de mémoire, en octets, qui doit être vidée par le nettoyage en arrière-plan.|ServerResourceType|
|CleanerMemoryNonshrinkable|Mémoire : mémoire de nettoyage non réductible|Octets|Moyenne|Quantité de mémoire, en octets, qui ne doit pas être vidée par le nettoyage en arrière-plan.|ServerResourceType|
|MemoryUsage|Mémoire : Utilisation de la mémoire|Octets|Moyenne|Utilisation de la mémoire du processus serveur telle qu’utilisée dans le calcul du coût de la mémoire de nettoyage. Équivaut au compteur Process\PrivateBytes, plus la taille des données mappées en mémoire, en ignorant la mémoire mappée ou allouée par le moteur d’analyse de mémoire xVelocity (VertiPaq) dépassant la limite de mémoire du moteur xVelocity.|ServerResourceType|
|MemoryLimitHard|Mémoire : limite inconditionnelle de la mémoire|Octets|Moyenne|Limite de mémoire physique, du fichier de configuration.|ServerResourceType|
|MemoryLimitHigh|Mémoire : limite haute de la mémoire|Octets|Moyenne|Limite de mémoire élevée, du fichier de configuration.|ServerResourceType|
|MemoryLimitLow|Mémoire : limite basse de la mémoire|Octets|Moyenne|Limite de mémoire basse, du fichier de configuration.|ServerResourceType|
|MemoryLimitVertiPaq|Mémoire : limite de la mémoire VertiPaq|Octets|Moyenne|Limite en mémoire, du fichier de configuration.|ServerResourceType|
|Quota|Mémoire : Quota|Octets|Moyenne|Quota de mémoire actuel, en octets. Le quota de mémoire est également appelé réserve de mémoire ou d’allocation.|ServerResourceType|
|QuotaBlocked|Mémoire : quota bloqué|Nombre|Moyenne|Nombre actuel de requêtes de quota qui sont bloquées en attendant la libération d’autres quotas de mémoire.|ServerResourceType|
|VertiPaqNonpaged|Mémoire : mémoire non paginée VertiPaq|Octets|Moyenne|Octets de mémoire verrouillée dans la plage de travail pour utilisation par le moteur en mémoire.|ServerResourceType|
|VertiPaqPaged|Mémoire : mémoire paginée VertiPaq|Octets|Moyenne|Octets de mémoire paginée utilisée pour les données en mémoire.|ServerResourceType|
|RowsReadPerSec|Traitement : lignes lues par seconde|CountPerSecond|Moyenne|Taux de lignes lues à partir de toutes les bases de données relationnelles.|ServerResourceType|
|RowsConvertedPerSec|Traitement : lignes converties par seconde|CountPerSecond|Moyenne|Taux de lignes converties lors du traitement.|ServerResourceType|
|RowsWrittenPerSec|Traitement : lignes écrites par seconde|CountPerSecond|Moyenne|Taux de lignes écrites lors du traitement.|ServerResourceType|
|CommandPoolBusyThreads|Threads : threads occupés du pool de commandes|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads de commandes.|ServerResourceType|
|CommandPoolIdleThreads|Threads : threads inactifs du pool de commandes|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads de commandes.|ServerResourceType|
|LongParsingBusyThreads|Threads : threads occupés d'analyse longue|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads d’analyse longue.|ServerResourceType|
|LongParsingIdleThreads|Threads : threads inactifs d'analyse longue|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads d’analyse longue.|ServerResourceType|
|LongParsingJobQueueLength|Threads : longueur de la file d'attente des travaux d'analyse longue|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse longue.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads : threads des travaux d'E/S occupés du pool de traitement|Nombre|Moyenne|Nombre de threads pour les travaux d’E/S en cours d’exécution dans le pool de threads de traitement.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads : threads autres que les threads d'E/S occupés du pool de traitement|Nombre|Moyenne|Nombre de threads pour les travaux autres que d’E/S en cours d’exécution dans le pool de threads de traitement.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads : longueur de la file des travaux d'E/S du pool de traitement|Nombre|Moyenne|Nombre de travaux d’E/S contenus dans la file d’attente du pool de threads de traitement.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads : threads des travaux d'E/S inactifs du pool de traitement|Nombre|Moyenne|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads : threads autres que les threads d'E/S inactifs du pool de traitement|Nombre|Moyenne|Nombre de threads inactifs le pool de threads de traitement dédiés aux travaux autres qu’E/S.|ServerResourceType|
|QueryPoolIdleThreads|Threads : threads inactifs du pool de requêtes|Nombre|Moyenne|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|ServerResourceType|
|QueryPoolJobQueueLength|Threads : longueur de la file d'attente des travaux du pool de requêtes|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de requêtes.|ServerResourceType|
|ShortParsingBusyThreads|Threads : threads occupés d'analyse courte|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads d’analyse courte.|ServerResourceType|
|ShortParsingIdleThreads|Threads : threads inactifs d'analyse courte|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads d’analyse courte.|ServerResourceType|
|ShortParsingJobQueueLength|Threads : longueur de la file d'attente des travaux d'analyse courte|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse courte.|ServerResourceType|
|memory_thrashing_metric|Vidage de mémoire|Pourcentage|Moyenne|Vidage de mémoire moyenne.|ServerResourceType|
|mashup_engine_qpu_metric|QPU du moteur M|Nombre|Moyenne|Utilisation des QPU par les processus de moteur mashup|ServerResourceType|
|mashup_engine_memory_metric|Mémoire du moteur M|Octets|Moyenne|Utilisation de la mémoire par les processus de moteur mashup|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalRequests|Nombre total de demandes de la passerelle|Nombre|Total|Nombre de demandes de la passerelle|Emplacement, nom d’hôte|
|SuccessfulRequests|Demandes de la passerelle ayant abouti|Nombre|Total|Nombre de demandes de la passerelle ayant abouti|Emplacement, nom d’hôte|
|UnauthorizedRequests|Demandes de la passerelle non autorisées|Nombre|Total|Nombre de demandes de la passerelle non autorisées|Emplacement, nom d’hôte|
|FailedRequests|Demandes de la passerelle ayant échoué|Nombre|Total|Nombre de défaillances des demandes de la passerelle|Emplacement, nom d’hôte|
|OtherRequests|Autres demandes de la passerelle|Nombre|Total|Nombre d’autres demandes de la passerelle|Emplacement, nom d’hôte|
|Duration|Durée globale des demandes de passerelle|Millisecondes|Moyenne|Durée globale des demandes de passerelle en millisecondes|Emplacement, nom d’hôte|
|Capacité|Capacité|Pourcentage|Moyenne|Métrique d’utilisation pour le service ApiManagement|Lieu|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalJob|Nombre total de travaux|Nombre|Total|Nombre total de travaux|Runbook, état|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CoreCount|Nombre de cœurs dédiés|Nombre|Total|Nombre total de cœurs dédiés dans le compte Batch|Aucune dimension|
|TotalNodeCount|Nombre de nœuds dédiés|Nombre|Total|Nombre total de nœuds dédiés dans le compte Batch|Aucune dimension|
|LowPriorityCoreCount|Nombre de cœurs à priorité basse|Nombre|Total|Nombre total de cœurs à priorité basse dans le compte Batch|Aucune dimension|
|TotalLowPriorityNodeCount|Nombre de nœuds à priorité basse|Nombre|Total|Nombre total de nœuds à priorité basse dans le compte Batch|Aucune dimension|
|CreatingNodeCount|Nombre de nœuds créés|Nombre|Total|Nombre de nœuds en cours de création|Aucune dimension|
|StartingNodeCount|Nombre de nœuds de départ|Nombre|Total|Nœuds de nœuds de départ|Aucune dimension|
|WaitingForStartTaskNodeCount|Nombre de nœuds en attente de démarrage de tâche|Nombre|Total|Nombre de nœuds en attente de la fin d’une tâche de démarrage|Aucune dimension|
|StartTaskFailedNodeCount|Nombre de nœuds pour lesquels le démarrage d’une tâche a échoué|Nombre|Total|Le nombre de nœuds pour lesquels le démarrage d’une tâche a échoué|Aucune dimension|
|IdleNodeCount|Nombre de nœuds inactifs|Nombre|Total|Le nombre de nœuds inactifs|Aucune dimension|
|OfflineNodeCount|Nombre de nœuds hors ligne|Nombre|Total|Le nombre de nœuds hors ligne|Aucune dimension|
|RebootingNodeCount|Nombre de nœuds en cours de redémarrage|Nombre|Total|Le nombre de nœuds en cours de redémarrage|Aucune dimension|
|ReimagingNodeCount|Nombre de nœuds en cours de réimageage|Nombre|Total|Le nombre de nœuds en cours de réimageage|Aucune dimension|
|RunningNodeCount|Nombre de nœuds en cours d’exécution|Nombre|Total|Le nombre de nœuds en cours d’exécution|Aucune dimension|
|LeavingPoolNodeCount|Nombre de nœuds quittant le pool|Nombre|Total|Le nombre de nœuds quittant le pool|Aucune dimension|
|UnusableNodeCount|Nombre de nœuds inutilisables|Nombre|Total|Le nombre de nœuds inutilisables|Aucune dimension|
|PreemptedNodeCount|Nombre de nœuds reportés|Nombre|Total|Nombre de nœuds reportés|Aucune dimension|
|TaskStartEvent|Événements de lancement de tâche|Nombre|Total|Nombre total de tâches ayant démarré|Aucune dimension|
|TaskCompleteEvent|Événements de fin de tâche|Nombre|Total|Le nombre total de tâches terminées|Aucune dimension|
|TaskFailEvent|Événements d’échec de tâches|Nombre|Total|Le nombre total de tâches terminées dans un état d’échec|Aucune dimension|
|PoolCreateEvent|Événements de création de pool|Nombre|Total|Nombre total de pools créés|Aucune dimension|
|PoolResizeStartEvent|Événements de démarrage de redimensionnement de pool|Nombre|Total|Nombre total de redimensionnements de pool ayant démarré|Aucune dimension|
|PoolResizeCompleteEvent|Événements de fin de redimensionnement de pool|Nombre|Total|Nombre total de redimensionnements de pool terminés|Aucune dimension|
|PoolDeleteStartEvent|Événements de démarrage de suppression de pool|Nombre|Total|Nombre total de suppressions de pool ayant démarré|Aucune dimension|
|PoolDeleteCompleteEvent|Événements de suppression de pool terminés|Nombre|Total|Nombre total de suppressions de pool terminées|Aucune dimension|
|JobDeleteCompleteEvent|Événements de fin de suppression de travail|Nombre|Total|Nombre total de travaux correctement supprimés.|Aucune dimension|
|JobDeleteStartEvent|Événements de démarrage de suppression de travail|Nombre|Total|Nombre total de travaux demandés pour être supprimés.|Aucune dimension|
|JobDisableCompleteEvent|Événements de fin de désactivation de travail|Nombre|Total|Nombre total de travaux correctement désactivés.|Aucune dimension|
|JobDisableStartEvent|Événements de démarrage de désactivation de travail|Nombre|Total|Nombre total de travaux demandés pour être désactivés.|Aucune dimension|
|JobStartEvent|Événements de démarrage de travail|Nombre|Total|Nombre total de travaux correctement démarrés.|Aucune dimension|
|JobTerminateCompleteEvent|Événements de fin de travail terminé|Nombre|Total|Nombre total de travaux correctement terminés.|Aucune dimension|
|JobTerminateCompleteEvent|Événements de démarrage de fin de travail|Nombre|Total|Nombre total de travaux demandés pour être terminés.|Aucune dimension|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|connectedclients|Clients connectés|Nombre|Maximale||ShardId|
|totalcommandsprocessed|Total des opérations|Nombre|Total||ShardId|
|cachehits|Présences dans le cache|Nombre|Total||ShardId|
|cachemisses|Absences dans le cache|Nombre|Total||ShardId|
|getcommands|Gets|Nombre|Total||ShardId|
|setcommands|Sets|Nombre|Total||ShardId|
|operationsPerSecond|Opérations par seconde|Nombre|Maximale||ShardId|
|evictedkeys|Clés exclues|Nombre|Total||ShardId|
|totalkeys|Nombre total de clés|Nombre|Maximale||ShardId|
|expiredkeys|Clés expirées|Nombre|Total||ShardId|
|usedmemory|Mémoire utilisée|Octets|Maximale||ShardId|
|usedmemorypercentage|Pourcentage de mémoire utilisé|Pourcentage|Maximale||ShardId|
|usedmemoryRss|Taille de la mémoire résidente utilisée|Octets|Maximale||ShardId|
|serverLoad|Charge du serveur|Pourcentage|Maximale||ShardId|
|cacheWrite|Cache d’écriture|BytesPerSecond|Maximale||ShardId|
|cacheRead|Lecture du cache|BytesPerSecond|Maximale||ShardId|
|percentProcessorTime|UC|Pourcentage|Maximale||ShardId|
|cacheLatency|Latence de cache en microsecondes (préversion)|Nombre|Moyenne||ShardId, SampleType|
|errors|Errors|Nombre|Maximale||ShardId, ErrorType|
|connectedclients0|Clients connectés (Shard 0)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed0|Total des opérations (Shard 0)|Nombre|Total||Aucune dimension|
|cachehits0|Présences dans le cache (Shard 0)|Nombre|Total||Aucune dimension|
|cachemisses0|Absences dans le cache (Shard 0)|Nombre|Total||Aucune dimension|
|getcommands0|Gets (Shard 0)|Nombre|Total||Aucune dimension|
|setcommands0|Sets (Shard 0)|Nombre|Total||Aucune dimension|
|operationsPerSecond0|Opérations par seconde (Partition 0)|Nombre|Maximale||Aucune dimension|
|evictedkeys0|Clés exclues (Shard 0)|Nombre|Total||Aucune dimension|
|totalkeys0|Nombre total de clés (Shard 0)|Nombre|Maximale||Aucune dimension|
|expiredkeys0|Clés expirées (Shard 0)|Nombre|Total||Aucune dimension|
|usedmemory0|Mémoire utilisée (Shard 0)|Octets|Maximale||Aucune dimension|
|usedmemoryRss0|Mémoire résidente utilisée (Shard 0)|Octets|Maximale||Aucune dimension|
|serverLoad0|Charge du serveur (Shard 0)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite0|Cache d’écriture (Shard 0)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead0|Cache de lecture (Shard 0)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime0|UC (Shard 0)|Pourcentage|Maximale||Aucune dimension|
|connectedclients1|Clients connectés (Shard 1)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed1|Total des opérations (Shard 1)|Nombre|Total||Aucune dimension|
|cachehits1|Présences dans le cache (Shard 1)|Nombre|Total||Aucune dimension|
|cachemisses1|Absences dans le cache (Shard 1)|Nombre|Total||Aucune dimension|
|getcommands1|Gets (Shard 1)|Nombre|Total||Aucune dimension|
|setcommands1|Sets (Shard 1)|Nombre|Total||Aucune dimension|
|operationsPerSecond1|Opérations par seconde (Partition 1)|Nombre|Maximale||Aucune dimension|
|evictedkeys1|Clés exclues (Shard 1)|Nombre|Total||Aucune dimension|
|totalkeys1|Nombre total de clés (Shard 1)|Nombre|Maximale||Aucune dimension|
|expiredkeys1|Clés expirées (Shard 1)|Nombre|Total||Aucune dimension|
|usedmemory1|Mémoire utilisée (Shard 1)|Octets|Maximale||Aucune dimension|
|usedmemoryRss1|Mémoire résidente utilisée (Shard 1)|Octets|Maximale||Aucune dimension|
|serverLoad1|Charge du serveur (Shard 1)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite1|Cache d’écriture (Shard 1)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead1|Lecture du cache (Shard 1)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime1|UC (Shard 1)|Pourcentage|Maximale||Aucune dimension|
|connectedclients2|Clients connectés (Shard 2)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed2|Total des opérations (Shard 2)|Nombre|Total||Aucune dimension|
|cachehits2|Présences dans le cache (Shard 2)|Nombre|Total||Aucune dimension|
|cachemisses2|Absences dans le cache (Shard 2)|Nombre|Total||Aucune dimension|
|getcommands2|Gets (Shard 2)|Nombre|Total||Aucune dimension|
|setcommands2|Sets (Shard 2)|Nombre|Total||Aucune dimension|
|operationsPerSecond2|Opérations par seconde (Partition 2)|Nombre|Maximale||Aucune dimension|
|evictedkeys2|Clés exclues (Shard 2)|Nombre|Total||Aucune dimension|
|totalkeys2|Nombre total de clés (Shard 2)|Nombre|Maximale||Aucune dimension|
|expiredkeys2|Clés expirées (Shard 2)|Nombre|Total||Aucune dimension|
|usedmemory2|Mémoire utilisée (Shard 2)|Octets|Maximale||Aucune dimension|
|usedmemoryRss2|Mémoire résidente utilisée (Shard 2)|Octets|Maximale||Aucune dimension|
|serverLoad2|Charge du serveur (Shard 2)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite2|Cache d’écriture (Shard 2)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead2|Lecture du cache (Shard 2)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime2|UC (Shard 2)|Pourcentage|Maximale||Aucune dimension|
|connectedclients3|Clients connectés (Shard 3)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed3|Total des opérations (Shard 3)|Nombre|Total||Aucune dimension|
|cachehits3|Présences dans le cache (Shard 3)|Nombre|Total||Aucune dimension|
|cachemisses3|Absences dans le cache (Shard 3)|Nombre|Total||Aucune dimension|
|getcommands3|Gets (Shard 3)|Nombre|Total||Aucune dimension|
|setcommands3|Sets (Shard 3)|Nombre|Total||Aucune dimension|
|operationsPerSecond3|Opérations par seconde (Partition 3)|Nombre|Maximale||Aucune dimension|
|evictedkeys3|Clés exclues (Shard 3)|Nombre|Total||Aucune dimension|
|totalkeys3|Nombre total de clés (Shard 3)|Nombre|Maximale||Aucune dimension|
|expiredkeys3|Clés expirées (Shard 3)|Nombre|Total||Aucune dimension|
|usedmemory3|Mémoire utilisée (Shard 3)|Octets|Maximale||Aucune dimension|
|usedmemoryRss3|Mémoire résidente utilisée (Shard 3)|Octets|Maximale||Aucune dimension|
|serverLoad3|Charge du serveur (Shard 3)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite3|Cache d’écriture (Shard 3)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead3|Lecture du cache (Shard 3)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime3|UC (Shard 3)|Pourcentage|Maximale||Aucune dimension|
|connectedclients4|Clients connectés (Shard 4)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed4|Total des opérations (Shard 4)|Nombre|Total||Aucune dimension|
|cachehits4|Présences dans le cache (Shard 4)|Nombre|Total||Aucune dimension|
|cachemisses4|Absences dans le cache (Shard 4)|Nombre|Total||Aucune dimension|
|getcommands4|Gets (Shard 4)|Nombre|Total||Aucune dimension|
|setcommands4|Sets (Shard 4)|Nombre|Total||Aucune dimension|
|operationsPerSecond4|Opérations par seconde (Partition 4)|Nombre|Maximale||Aucune dimension|
|evictedkeys4|Clés exclues (Shard 4)|Nombre|Total||Aucune dimension|
|totalkeys4|Nombre total de clés (Shard 4)|Nombre|Maximale||Aucune dimension|
|expiredkeys4|Clés expirées (Shard 4)|Nombre|Total||Aucune dimension|
|usedmemory4|Mémoire utilisée (Shard 4)|Octets|Maximale||Aucune dimension|
|usedmemoryRss4|Mémoire résidente utilisée (Shard 4)|Octets|Maximale||Aucune dimension|
|serverLoad4|Charge du serveur (Shard 4)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite4|Cache d’écriture (Shard 4)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead4|Lecture du cache (Shard 4)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime4|UC (Shard 4)|Pourcentage|Maximale||Aucune dimension|
|connectedclients5|Clients connectés (Shard 5)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed5|Total des opérations (Shard 5)|Nombre|Total||Aucune dimension|
|cachehits5|Présences dans le cache (Shard 5)|Nombre|Total||Aucune dimension|
|cachemisses5|Absences dans le cache (Shard 5)|Nombre|Total||Aucune dimension|
|getcommands5|Gets (Shard 5)|Nombre|Total||Aucune dimension|
|setcommands5|Sets (Shard 5)|Nombre|Total||Aucune dimension|
|operationsPerSecond5|Opérations par seconde (Partition 5)|Nombre|Maximale||Aucune dimension|
|evictedkeys5|Clés exclues (Shard 5)|Nombre|Total||Aucune dimension|
|totalkeys5|Nombre total de clés (Shard 5)|Nombre|Maximale||Aucune dimension|
|expiredkeys5|Clés expirées (Shard 5)|Nombre|Total||Aucune dimension|
|usedmemory5|Mémoire utilisée (Shard 5)|Octets|Maximale||Aucune dimension|
|usedmemoryRss5|Mémoire résidente utilisée (Shard 5)|Octets|Maximale||Aucune dimension|
|serverLoad5|Charge du serveur (Shard 5)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite5|Cache d’écriture (Shard 5)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead5|Lecture du cache (Shard 5)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime5|UC (Shard 5)|Pourcentage|Maximale||Aucune dimension|
|connectedclients6|Clients connectés (Shard 6)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed6|Total des opérations (Shard 6)|Nombre|Total||Aucune dimension|
|cachehits6|Présences dans le cache (Shard 6)|Nombre|Total||Aucune dimension|
|cachemisses6|Absences dans le cache (Shard 6)|Nombre|Total||Aucune dimension|
|getcommands6|Gets (Shard 6)|Nombre|Total||Aucune dimension|
|setcommands6|Sets (Shard 6)|Nombre|Total||Aucune dimension|
|operationsPerSecond6|Opérations par seconde (Partition 6)|Nombre|Maximale||Aucune dimension|
|evictedkeys6|Clés exclues (Shard 6)|Nombre|Total||Aucune dimension|
|totalkeys6|Nombre total de clés (Shard 6)|Nombre|Maximale||Aucune dimension|
|expiredkeys6|Clés expirées (Shard 6)|Nombre|Total||Aucune dimension|
|usedmemory6|Mémoire utilisée (Shard 6)|Octets|Maximale||Aucune dimension|
|usedmemoryRss6|Mémoire résidente utilisée (Shard 6)|Octets|Maximale||Aucune dimension|
|serverLoad6|Charge du serveur (Shard 6)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite6|Cache d’écriture (Shard 6)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead6|Lecture du cache (Shard 6)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime6|UC (Shard 6)|Pourcentage|Maximale||Aucune dimension|
|connectedclients7|Clients connectés (Shard 7)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed7|Total des opérations (Shard 7)|Nombre|Total||Aucune dimension|
|cachehits7|Présences dans le cache (Shard 7)|Nombre|Total||Aucune dimension|
|cachemisses7|Absences dans le cache (Shard 7)|Nombre|Total||Aucune dimension|
|getcommands7|Gets (Shard 7)|Nombre|Total||Aucune dimension|
|setcommands7|Sets (Shard 7)|Nombre|Total||Aucune dimension|
|operationsPerSecond7|Opérations par seconde (Partition 7)|Nombre|Maximale||Aucune dimension|
|evictedkeys7|Clés exclues (Shard 7)|Nombre|Total||Aucune dimension|
|totalkeys7|Nombre total de clés (Shard 7)|Nombre|Maximale||Aucune dimension|
|expiredkeys7|Clés expirées (Shard 7)|Nombre|Total||Aucune dimension|
|usedmemory7|Mémoire utilisée (Shard 7)|Octets|Maximale||Aucune dimension|
|usedmemoryRss7|Mémoire résidente utilisée (Shard 7)|Octets|Maximale||Aucune dimension|
|serverLoad7|Charge du serveur (Shard 7)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite7|Cache d’écriture (Shard 7)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead7|Lecture du cache (Shard 7)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime7|UC (Shard 7)|Pourcentage|Maximale||Aucune dimension|
|connectedclients8|Clients connectés (Shard 8)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed8|Total des opérations (Shard 8)|Nombre|Total||Aucune dimension|
|cachehits8|Présences dans le cache (Shard 8)|Nombre|Total||Aucune dimension|
|cachemisses8|Absences dans le cache (Shard 8)|Nombre|Total||Aucune dimension|
|getcommands8|Gets (Shard 8)|Nombre|Total||Aucune dimension|
|setcommands8|Sets (Shard 8)|Nombre|Total||Aucune dimension|
|operationsPerSecond8|Opérations par seconde (Partition 8)|Nombre|Maximale||Aucune dimension|
|evictedkeys8|Clés exclues (Shard 8)|Nombre|Total||Aucune dimension|
|totalkeys8|Nombre total de clés (Shard 8)|Nombre|Maximale||Aucune dimension|
|expiredkeys8|Clés expirées (Shard 8)|Nombre|Total||Aucune dimension|
|usedmemory8|Mémoire utilisée (Shard 8)|Octets|Maximale||Aucune dimension|
|usedmemoryRss8|Mémoire résidente utilisée (Shard 8)|Octets|Maximale||Aucune dimension|
|serverLoad8|Charge du serveur (Shard 8)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite8|Cache d’écriture (Shard 8)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead8|Lecture du cache (Shard 8)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime8|UC (Shard 8)|Pourcentage|Maximale||Aucune dimension|
|connectedclients9|Clients connectés (Shard 9)|Nombre|Maximale||Aucune dimension|
|totalcommandsprocessed9|Total des opérations (Shard 9)|Nombre|Total||Aucune dimension|
|cachehits9|Présences dans le cache (Shard 9)|Nombre|Total||Aucune dimension|
|cachemisses9|Absences dans le cache (Shard 9)|Nombre|Total||Aucune dimension|
|getcommands9|Gets (Shard 9)|Nombre|Total||Aucune dimension|
|setcommands9|Sets (Shard 9)|Nombre|Total||Aucune dimension|
|operationsPerSecond9|Opérations par seconde (Partition 9)|Nombre|Maximale||Aucune dimension|
|evictedkeys9|Clés exclues (Shard 9)|Nombre|Total||Aucune dimension|
|totalkeys9|Nombre total de clés (Shard 9)|Nombre|Maximale||Aucune dimension|
|expiredkeys9|Clés expirées (Shard 9)|Nombre|Total||Aucune dimension|
|usedmemory9|Mémoire utilisée (Shard 9)|Octets|Maximale||Aucune dimension|
|usedmemoryRss9|Mémoire résidente utilisée (Shard 9)|Octets|Maximale||Aucune dimension|
|serverLoad9|Charge du serveur (Shard 9)|Pourcentage|Maximale||Aucune dimension|
|cacheWrite9|Cache d’écriture (Shard 9)|BytesPerSecond|Maximale||Aucune dimension|
|cacheRead9|Cache de lecture (Shard 9)|BytesPerSecond|Maximale||Aucune dimension|
|percentProcessorTime9|UC (Shard 9)|Pourcentage|Maximale||Aucune dimension|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles.|Aucune dimension|
|Network In|Network In|Octets|Total|Nombre d’octets reçus sur toutes les interfaces réseau par les machines virtuelles (trafic entrant).|Aucune dimension|
|Network Out|Network Out|Octets|Total|Nombre d’octets envoyés sur toutes les interfaces réseau par les machines virtuelles (trafic sortant).|Aucune dimension|
|Disk Read Bytes/Sec|Lecture du disque|BytesPerSecond|Moyenne|Moyenne d’octets lus à partir du disque pendant la période d’analyse.|Aucune dimension|
|Disk Write Bytes/Sec|Écriture sur le disque|BytesPerSecond|Moyenne|Moyenne d’octets écrits sur le disque pendant la période d’analyse.|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde.|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture sur disque par seconde.|Aucune dimension|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles.|RoleInstanceId|
|Network In|Network In|Octets|Total|Nombre d’octets reçus sur toutes les interfaces réseau par les machines virtuelles (trafic entrant).|RoleInstanceId|
|Network Out|Network Out|Octets|Total|Nombre d’octets envoyés sur toutes les interfaces réseau par les machines virtuelles (trafic sortant).|RoleInstanceId|
|Disk Read Bytes/Sec|Lecture du disque|BytesPerSecond|Moyenne|Moyenne d’octets lus à partir du disque pendant la période d’analyse.|RoleInstanceId|
|Disk Write Bytes/Sec|Écriture sur le disque|BytesPerSecond|Moyenne|Moyenne d’octets écrits sur le disque pendant la période d’analyse.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture sur disque par seconde.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalCalls|Nombre total d’appels|Nombre|Total|Nombre total d’appels.|Aucune dimension|
|SuccessfulCalls|Appels réussis|Nombre|Total|Nombre d’appels réussis.|Aucune dimension|
|TotalErrors|Nombre total d’erreurs|Nombre|Total|Nombre total d’appels avec réponse d’erreur (code de réponse HTTP : 4xx ou 5xx).|Aucune dimension|
|BlockedCalls|Appels bloqués|Nombre|Total|Nombre d’appels ayant dépassé la limite de débit ou de quota.|Aucune dimension|
|ServerErrors|Erreurs de serveur|Nombre|Total|Nombre d’appels avec erreur interne du service (code de réponse HTTP : 5xx).|Aucune dimension|
|ClientErrors|Erreurs de client|Nombre|Total|Nombre d’appels avec erreur côté client (code de réponse HTTP : 4xx).|Aucune dimension|
|DataIn|Données entrantes|Octets|Total|Taille des données entrantes en octets.|Aucune dimension|
|DataOut|Données sortantes|Octets|Total|Taille des données sortantes en octets.|Aucune dimension|
|Latence|Latence|Millisecondes|Moyenne|Latence en millisecondes.|Aucune dimension|
|CharactersTranslated|Caractères traduits|Nombre|Total|Nombre total de caractères dans la requête de texte entrante.|Aucune dimension|
|SpeechSessionDuration|Durée de la session vocale|Secondes|Total|Durée totale de la session vocale en secondes.|Aucune dimension|
|TotalTransactions|Nombre total de transactions|Nombre|Total|Nombre total de transactions.|Aucune dimension|
|TotalTokenCalls|Total d’appels de jeton|Nombre|Total|Nombre total d’appels de jeton.|Aucune dimension|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|Aucune dimension|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|Aucune dimension|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|Aucune dimension|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|Aucune dimension|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|Aucune dimension|
|Crédits de processeurs restants|Crédits de processeurs restants|Nombre|Moyenne|Nombre total de crédits pouvant être consommés|Aucune dimension|
|Crédits de processeur consommés|Crédits de processeur consommés|Nombre|Moyenne|Nombre total de crédits consommés par la machine virtuelle|Aucune dimension|
|Octets lus/s par disque|Octets lus/s sur disque de données (préversion)|CountPerSecond|Moyenne|Total des octets/s lus sur un seul disque pendant la période de surveillance|SlotId|
|Octets écrits/s par disque|Octets/s écrits sur disque de données (préversion)|CountPerSecond|Moyenne|Total des octets/s écrits sur un seul disque pendant la période de surveillance|SlotId|
|Opérations de lecture/s par disque|Opérations de lecture/s sur disque de données (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de la lecture sur un seul disque pendant la période de surveillance|SlotId|
|Opérations d’écriture/s par disque|Opérations d’écriture/s sur disque de données (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de l’écriture sur un seul disque pendant la période de surveillance|SlotId|
|QD par disque|QD du disque de données (préversion)|Nombre|Moyenne|Longueur de file d’attente (ou QD) du disque de données|SlotId|
|Octets lus/s sur système d’exploitation par disque|Octets lus/s sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des octets/s lus sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|Octets écrits/s sur système d’exploitation par disque|Octets/s écrits sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des octets/s écrits sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|Opérations de lecture/s sur système d’exploitation par disque|Opérations de lecture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de la lecture sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|Opérations d’écriture/s sur système d’exploitation par disque|Opérations d’écriture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de l’écriture sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|QD sur système d’exploitation par disque|QD du disque de système d’exploitation (préversion)|Nombre|Moyenne|Longueur de file d’attente (ou QD) du disque du système d’exploitation|Aucune dimension|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|Aucune dimension|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|Aucune dimension|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|Aucune dimension|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|Aucune dimension|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|Aucune dimension|
|Crédits de processeurs restants|Crédits de processeurs restants|Nombre|Moyenne|Nombre total de crédits pouvant être consommés|Aucune dimension|
|Crédits de processeur consommés|Crédits de processeur consommés|Nombre|Moyenne|Nombre total de crédits consommés par la machine virtuelle|Aucune dimension|
|Octets lus/s par disque|Octets lus/s sur disque de données (préversion)|CountPerSecond|Moyenne|Total des octets/s lus sur un seul disque pendant la période de surveillance|SlotId|
|Octets écrits/s par disque|Octets/s écrits sur disque de données (préversion)|CountPerSecond|Moyenne|Total des octets/s écrits sur un seul disque pendant la période de surveillance|SlotId|
|Opérations de lecture/s par disque|Opérations de lecture/s sur disque de données (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de la lecture sur un seul disque pendant la période de surveillance|SlotId|
|Opérations d’écriture/s par disque|Opérations d’écriture/s sur disque de données (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de l’écriture sur un seul disque pendant la période de surveillance|SlotId|
|QD par disque|QD du disque de données (préversion)|Nombre|Moyenne|Longueur de file d’attente (ou QD) du disque de données|SlotId|
|Octets lus/s sur système d’exploitation par disque|Octets lus/s sur disque du système d’exploitation|CountPerSecond|Moyenne|Total des octets/s lus sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|Octets écrits/s sur système d’exploitation par disque|Octets/s écrits sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des octets/s écrits sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|Opérations de lecture/s sur système d’exploitation par disque|Opérations de lecture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de la lecture sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|Opérations d’écriture/s sur système d’exploitation par disque|Opérations d’écriture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de l’écriture sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|QD sur système d’exploitation par disque|QD du disque de système d’exploitation (préversion)|Nombre|Moyenne|Longueur de file d’attente (ou QD) du disque du système d’exploitation|Aucune dimension|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|Aucune dimension|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|Aucune dimension|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|Aucune dimension|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|Aucune dimension|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|Aucune dimension|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|Aucune dimension|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|Aucune dimension|
|Crédits de processeurs restants|Crédits de processeurs restants|Nombre|Moyenne|Nombre total de crédits pouvant être consommés|Aucune dimension|
|Crédits de processeur consommés|Crédits de processeur consommés|Nombre|Moyenne|Nombre total de crédits consommés par la machine virtuelle|Aucune dimension|
|Octets lus/s par disque|Octets lus/s sur disque de données (préversion)|CountPerSecond|Moyenne|Total des octets/s lus sur un seul disque pendant la période de surveillance|SlotId|
|Octets écrits/s par disque|Octets/s écrits sur disque de données (préversion)|CountPerSecond|Moyenne|Total des octets/s écrits sur un seul disque pendant la période de surveillance|SlotId|
|Opérations de lecture/s par disque|Opérations de lecture/s sur disque de données (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de la lecture sur un seul disque pendant la période de surveillance|SlotId|
|Opérations d’écriture/s par disque|Opérations d’écriture/s sur disque de données (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de l’écriture sur un seul disque pendant la période de surveillance|SlotId|
|QD par disque|QD du disque de données (préversion)|Nombre|Moyenne|Longueur de file d’attente (ou QD) du disque de données|SlotId|
|Octets lus/s sur système d’exploitation par disque|Octets lus/s sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des octets/s lus sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|Octets écrits/s sur système d’exploitation par disque|Octets/s écrits sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des octets/s écrits sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|Opérations de lecture/s sur système d’exploitation par disque|Opérations de lecture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de la lecture sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|Opérations d’écriture/s sur système d’exploitation par disque|Opérations d’écriture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Moyenne|Total des IOPS effectuées lors de l’écriture sur un seul disque pendant la période de surveillance du disque du système d’exploitation|Aucune dimension|
|QD sur système d’exploitation par disque|QD du disque de système d’exploitation (préversion)|Nombre|Moyenne|Longueur de file d’attente (ou QD) du disque du système d’exploitation|Aucune dimension|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuUsage|Utilisation du processeur|Nombre|Moyenne|Utilisation du processeur sur tous les cœurs en millicores.|containerName|
|MemoryUsage|Utilisation de la mémoire|Octets|Moyenne|Utilisation de la mémoire totale en octets.|containerName|
|NetworkBytesReceivedPerSecond|Octets réseau reçus par seconde|Octets|Moyenne|Octets réseau reçus par seconde.|Aucune dimension|
|NetworkBytesTransmittedPerSecond|Octets réseau transmis par seconde|Octets|Moyenne|Octets réseau transmis par seconde.|Aucune dimension|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Nombre total de cœurs d’unité centrale disponibles dans un cluster géré|Nombre|Total|Nombre total de cœurs d’unité centrale disponibles dans un cluster géré|Aucune dimension|
|kube_node_status_allocatable_memory_bytes|Quantité totale de mémoire disponible dans un cluster géré|Octets|Total|Quantité totale de mémoire disponible dans un cluster géré|Aucune dimension|
|kube_pod_status_ready|Nombre de pods en état Prêt|Nombre|Total|Nombre de pods en état Prêt|espace de noms, pod|
|kube_node_status_condition|États pour les différentes conditions de nœud|Nombre|Total|États pour les différentes conditions de nœud|condition, état, nœud|
|kube_pod_status_phase|Nombre de pods par phase|Nombre|Total|Nombre de pods par phase|phase, espace de noms, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|DCIApiCalls|Appels de l’API Insights client|Nombre|Total||Aucune dimension|
|DCIMappingImportOperationSuccessfulLines|Mappage des lignes de réussite d’opération d’importation|Nombre|Total||Aucune dimension|
|DCIMappingImportOperationFailedLines|Mappage des lignes en échec d’opération d’importation|Nombre|Total||Aucune dimension|
|DCIMappingImportOperationTotalLines|Mappage du total des lignes d’opération d’importation|Nombre|Total||Aucune dimension|
|DCIMappingImportOperationRuntimeInSeconds|Mappage d’exécution d’opération d’importation en secondes|Secondes|Total||Aucune dimension|
|DCIOutboundProfileExportSucceeded|Exportation réussie de profil de sortie|Nombre|Total||Aucune dimension|
|DCIOutboundProfileExportFailed|Échec de l’exportation de profil de sortie|Nombre|Total||Aucune dimension|
|DCIOutboundProfileExportDuration|Durée d’exportation de profil de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundKpiExportSucceeded|Exportation réussie de l’indicateur KPI sortant|Nombre|Total||Aucune dimension|
|DCIOutboundKpiExportFailed|Échec de l’exportation de l’indicateur KPI sortant|Nombre|Total||Aucune dimension|
|DCIOutboundKpiExportDuration|Durée de l’exportation de l’indicateur KPI sortant|Secondes|Total||Aucune dimension|
|DCIOutboundKpiExportStarted|Exportation démarrée de l’indicateur KPI sortant|Secondes|Total||Aucune dimension|
|DCIOutboundKpiRecordCount|Nombre d’enregistrements KPI sortants|Secondes|Total||Aucune dimension|
|DCIOutboundProfileExportCount|Nombre d’exportations de profils de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundInitialProfileExportFailed|Échec de l’exportation de profil initial de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundInitialProfileExportSucceeded|Réussite de l’exportation de profil initial de sortie|Secondes|Total||Aucune dimension|
|DCIOutboundInitialKpiExportFailed|Échec de l’exportation de l’indicateur KPI initial sortant|Secondes|Total||Aucune dimension|
|DCIOutboundInitialKpiExportSucceeded|Réussite de l’exportation de l’indicateur KPI initial sortant|Secondes|Total||Aucune dimension|
|DCIOutboundInitialProfileExportDurationInSeconds|Durée d’exportation de profil initial de sortie en secondes|Secondes|Total||Aucune dimension|
|AdlaJobForStandardKpiFailed|Échec du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||Aucune dimension|
|AdlaJobForStandardKpiTimeOut|Délai d’expiration du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||Aucune dimension|
|AdlaJobForStandardKpiCompleted|Achèvement du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||Aucune dimension|
|ImportASAValuesFailed|Nombre d’importations de valeurs ASA en échec|Nombre|Total||Aucune dimension|
|ImportASAValuesSucceeded|Nombre d’importations de valeurs ASA réussies|Nombre|Total||Aucune dimension|
|DCIProfilesCount|Nombre d’instances de profil|Nombre|Dernier||Aucune dimension|
|DCIInteractionsPerMonthCount|Nombre d’interactions par mois|Nombre|Dernier||Aucune dimension|
|DCIKpisCount|Nombre de KPI|Nombre|Dernier||Aucune dimension|
|DCISegmentsCount|Nombre de segments|Nombre|Dernier||Aucune dimension|
|DCIPredictiveMatchPoliciesCount|Nombre de correspondances prédictives|Nombre|Dernier||Aucune dimension|
|DCIPredictionsCount|Nombre de prédictions|Nombre|Dernier||Aucune dimension|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|FailedRuns|Exécutions échouées|Nombre|Total||pipelineName, activityName, windowEnd, windowStart|
|SuccessfulRuns|Exécutions réussies|Nombre|Total||pipelineName, activityName, windowEnd, windowStart|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métriques d’exécutions de pipeline ayant échoué|Nombre|Total||FailureType, Name|
|PipelineSucceededRuns|Métriques d’exécutions de pipeline ayant abouti|Nombre|Total||FailureType, Name|
|ActivityFailedRuns|Métriques d’exécutions d’activité ayant échoué|Nombre|Total||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Métriques d’exécutions d’activité ayant abouti|Nombre|Total||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Métriques d’exécutions de déclencheur ayant échoué|Nombre|Total||Name, FailureType|
|TriggerSucceededRuns|Métriques d’exécutions de déclencheur ayant abouti|Nombre|Total||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Utilisation du processeur du runtime d’intégration|Pourcentage|Moyenne||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Mémoire disponible du runtime d’intégration|Octets|Moyenne||IntegrationRuntimeName, NodeName|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|JobEndedSuccess|Travaux réussis|Nombre|Total|Nombre de travaux réussis|Aucune dimension|
|JobEndedFailure|Travaux en échec|Nombre|Total|Nombre de travaux en échec|Aucune dimension|
|JobEndedCancelled|Travaux annulés|Nombre|Total|Nombre de travaux annulés|Aucune dimension|
|JobAUEndedSuccess|Durée AU de réussite|Secondes|Total|Durée AU totale des travaux réussis.|Aucune dimension|
|JobAUEndedFailure|Durée AU d’échec|Secondes|Total|Durée AU totale des travaux en échec|Aucune dimension|
|JobAUEndedCancelled|Durée AU d’annulation|Secondes|Total|Durée AU totale des travaux annulés|Aucune dimension|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalStorage|Stockage total|Octets|Maximale|Volume total de données stockées dans le compte.|Aucune dimension|
|DataWritten|Données écrites|Octets|Total|Volume total de données écrites dans le compte.|Aucune dimension|
|DataRead|Données lues|Octets|Total|Volume total de données lues à partir du compte.|Aucune dimension|
|WriteRequests|Demandes d’écriture|Nombre|Total|Nombre de demandes d’écriture de données sur le compte.|Aucune dimension|
|ReadRequests|Demandes de lecture|Nombre|Total|Nombre de demandes de lecture de données sur le compte.|Aucune dimension|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Moyenne|Pourcentage d’UC|Aucune dimension|
|memory_percent|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Aucune dimension|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Moyenne|Pourcentage d’E/S|Aucune dimension|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|Aucune dimension|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|Aucune dimension|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|Aucune dimension|
|serverlog_storage_percent|Pourcentage de stockage du journal du serveur|Pourcentage|Moyenne|Pourcentage de stockage du journal du serveur|Aucune dimension|
|serverlog_storage_usage|Stockage du journal du serveur utilisé|Octets|Moyenne|Stockage du journal du serveur utilisé|Aucune dimension|
|serverlog_storage_limit|Limite de stockage du journal du serveur|Octets|Moyenne|Limite de stockage du journal du serveur|Aucune dimension|
|active_connections|Connexions actives|Nombre|Moyenne|Connexions actives|Aucune dimension|
|connections_failed|Connexions ayant échoué|Nombre|Total|Connexions ayant échoué|Aucune dimension|
|network_bytes_egress|Network Out|Octets|Total|Sortie réseau entre connexions actives|Aucune dimension|
|network_bytes_ingress|Network In|Octets|Total|Entrée réseau entre connexions actives|Aucune dimension|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Moyenne|Pourcentage d’UC|Aucune dimension|
|memory_percent|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Aucune dimension|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Moyenne|Pourcentage d’E/S|Aucune dimension|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|Aucune dimension|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|Aucune dimension|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|Aucune dimension|
|serverlog_storage_percent|Pourcentage de stockage du journal du serveur|Pourcentage|Moyenne|Pourcentage de stockage du journal du serveur|Aucune dimension|
|serverlog_storage_usage|Stockage du journal du serveur utilisé|Octets|Moyenne|Stockage du journal du serveur utilisé|Aucune dimension|
|serverlog_storage_limit|Limite de stockage du journal du serveur|Octets|Moyenne|Limite de stockage du journal du serveur|Aucune dimension|
|active_connections|Connexions actives|Nombre|Moyenne|Connexions actives|Aucune dimension|
|connections_failed|Connexions ayant échoué|Nombre|Total|Connexions ayant échoué|Aucune dimension|
|seconds_behind_master|Décalage de la réplication en secondes|Nombre|Moyenne|Décalage de la réplication en secondes|Aucune dimension|
|network_bytes_egress|Network Out|Octets|Total|Sortie réseau entre connexions actives|Aucune dimension|
|network_bytes_ingress|Network In|Octets|Total|Entrée réseau entre connexions actives|Aucune dimension|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Moyenne|Pourcentage d’UC|Aucune dimension|
|memory_percent|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Aucune dimension|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Moyenne|Pourcentage d’E/S|Aucune dimension|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|Aucune dimension|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|Aucune dimension|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|Aucune dimension|
|serverlog_storage_percent|Pourcentage de stockage du journal du serveur|Pourcentage|Moyenne|Pourcentage de stockage du journal du serveur|Aucune dimension|
|serverlog_storage_usage|Stockage du journal du serveur utilisé|Octets|Moyenne|Stockage du journal du serveur utilisé|Aucune dimension|
|serverlog_storage_limit|Limite de stockage du journal du serveur|Octets|Moyenne|Limite de stockage du journal du serveur|Aucune dimension|
|active_connections|Connexions actives|Nombre|Moyenne|Connexions actives|Aucune dimension|
|connections_failed|Connexions ayant échoué|Nombre|Total|Connexions ayant échoué|Aucune dimension|
|network_bytes_egress|Network Out|Octets|Total|Sortie réseau entre connexions actives|Aucune dimension|
|network_bytes_ingress|Network In|Octets|Total|Entrée réseau entre connexions actives|Aucune dimension|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentatives d’envoi de message de télémétrie|Nombre|Total|Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud à votre hub IoT|Aucune dimension|
|d2c.telemetry.ingress.success|Messages de télémétrie envoyés|Nombre|Total|Nombre de messages de télémétrie appareil vers cloud envoyés avec succès à votre hub IoT|Aucune dimension|
|c2d.commands.egress.complete.success|Commandes terminées|Nombre|Total|Nombre de commandes cloud vers appareil terminées avec succès par l’appareil|Aucune dimension|
|c2d.commands.egress.abandon.success|Commandes abandonnées|Nombre|Total|Nombre de commandes cloud vers appareil abandonnées par l’appareil|Aucune dimension|
|c2d.commands.egress.reject.success|Commandes rejetées|Nombre|Total|Nombre de commandes cloud vers appareil rejetées par l’appareil|Aucune dimension|
|devices.totalDevices|Nombre total d’appareils (déconseillé)|Nombre|Total|Nombre d’appareils enregistrés sur votre hub IoT|Aucune dimension|
|devices.connectedDevices.allProtocol|Appareils connectés (déconseillé) |Nombre|Total|Nombre d’appareils connectés à votre hub IoT|Aucune dimension|
|d2c.telemetry.egress.success|Routage : messages de télémétrie remis|Nombre|Total|Nombre de fois où des messages ont été correctement remis à tous les points de terminaison à l’aide du routage IoT Hub. Si un message est routé vers plusieurs points de terminaison, cette valeur augmente d’une unité pour chaque remise réussie. Si un message est routé plusieurs fois vers le même point de terminaison, cette valeur augmente d’une unité pour chaque remise réussie.|Aucune dimension|
|d2c.telemetry.egress.dropped|Routage : messages de télémétrie annulés |Nombre|Total|Nombre de fois où des messages ont été annulés par le routage IoT Hub en raison de points de terminaison morts. Cette valeur ne compte pas les messages remis à un itinéraire de secours, car les messages annulés n’y sont pas remis.|Aucune dimension|
|d2c.telemetry.egress.orphaned|Routage : messages de télémétrie orphelins |Nombre|Total|Nombre de fois où des messages ont été définis comme orphelins par le routage IoT Hub car ils ne correspondaient à aucune règle de routage (y compris la règle de secours). |Aucune dimension|
|d2c.telemetry.egress.invalid|Routage : messages de télémétrie incompatibles|Nombre|Total|Nombre de fois où le routage IoT Hub n’a pas réussi à remettre des messages en raison d’une incompatibilité avec le point de terminaison. Cette valeur n’inclut pas les nouvelles tentatives.|Aucune dimension|
|d2c.telemetry.egress.fallback|Routage : messages remis à l’itinéraire de secours|Nombre|Total|Nombre de fois où le routage IoT Hub a remis des messages au point de terminaison associé à l’itinéraire de secours.|Aucune dimension|
|d2c.endpoints.egress.eventHubs|Routage : messages remis à Event Hub|Nombre|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison Event Hub.|Aucune dimension|
|d2c.endpoints.latency.eventHubs|Routage : latence des messages d’Event Hub|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et dans un point de terminaison Event Hub.|Aucune dimension|
|d2c.endpoints.egress.serviceBusQueues|Routage : messages remis à la file d’attente Service Bus|Nombre|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages aux points de terminaison de file d’attente Service Bus.|Aucune dimension|
|d2c.endpoints.latency.serviceBusQueues|Routage : latence des messages de la file d’attente Service Bus|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de file d’attente Service Bus.|Aucune dimension|
|d2c.endpoints.egress.serviceBusTopics|Routage : messages remis à la rubrique Service Bus|Nombre|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison de rubrique Service Bus.|Aucune dimension|
|d2c.endpoints.latency.serviceBusTopics|Routage : latence des messages de la rubrique Service Bus|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de rubrique Service Bus.|Aucune dimension|
|d2c.endpoints.egress.builtIn.events|Routage : messages remis à des messages/événements|Nombre|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages au point de terminaison intégré (messages/événements).|Aucune dimension|
|d2c.endpoints.latency.builtIn.events|Routage : latence des messages de messages/d’événements|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans le point de terminaison intégré (messages/événements).|Aucune dimension|
|d2c.endpoints.egress.storage|Routage : messages remis au stockage|Nombre|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison de stockage.|Aucune dimension|
|d2c.endpoints.latency.storage|Routage : latence des messages du stockage|Millisecondes|Moyenne|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de stockage.|Aucune dimension|
|d2c.endpoints.egress.storage.bytes|Routage : données remises au stockage|Octets|Total|Quantité de données (octets) que le routage IoT Hub a remis aux points de terminaison de stockage.|Aucune dimension|
|d2c.endpoints.egress.storage.blobs|Routage : objets blob remis au stockage|Nombre|Total|Nombre de fois où le routage IoT Hub a remis des objets blob à des points de terminaison de stockage.|Aucune dimension|
|d2c.twin.read.success|Lectures de représentations réussies d’appareils|Nombre|Total|Total des lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.read.failure|Lectures de représentations d’appareils en échec|Nombre|Total|Total des lectures de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c.twin.read.size|Taille de la réponse des lectures de représentations des appareils|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.update.success|Mises à jour de représentations réussies d’appareils|Nombre|Total|Total des mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|d2c.twin.update.failure|Mises à jour de représentations d’appareils en échec|Nombre|Total|Total des mises à jour de représentations en échec initiées par un appareil.|Aucune dimension|
|d2c.twin.update.size|Taille des mises à jour de représentations d’appareils|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un appareil.|Aucune dimension|
|c2d.methods.success|Appels de méthode directe réussis|Nombre|Total|Total des appels de méthode directe réussis.|Aucune dimension|
|c2d.methods.failure|Appels de méthode directe en échec|Nombre|Total|Total des appels de méthode directe en échec.|Aucune dimension|
|c2d.methods.requestSize|Taille de demande des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les demandes de méthode directe réussies.|Aucune dimension|
|c2d.methods.responseSize|Taille de réponse des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les réponses de méthode directe réussies.|Aucune dimension|
|c2d.twin.read.success|Lectures de représentations réussies de serveur principal|Nombre|Total|Total des lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.read.failure|Lectures de représentations de serveur principal en échec|Nombre|Total|Total des lectures de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d.twin.read.size|Taille de la réponse des lectures de représentations de serveur principal|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.success|Mises à jour de représentations réussies de serveur principal|Nombre|Total|Total des mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.failure|Mises à jour de représentations de serveur principal en échec|Nombre|Total|Total des mises à jour de représentations en échec initiées par un serveur principal.|Aucune dimension|
|c2d.twin.update.size|Taille des mises à jour de représentations de serveur principal|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un serveur principal.|Aucune dimension|
|twinQueries.success|Requêtes de représentations réussies|Nombre|Total|Total des requêtes de représentations réussies.|Aucune dimension|
|twinQueries.failure|Requêtes de représentations en échec|Nombre|Total|Total des requêtes de représentations en échec.|Aucune dimension|
|twinQueries.resultSize|Taille du résultat des requêtes de représentations|Octets|Moyenne|Moyenne, minimum et maximum de la taille du résultat de toutes les requêtes de représentations réussies.|Aucune dimension|
|jobs.createTwinUpdateJob.success|Créations réussies des travaux de mises à jour de représentations|Nombre|Total|Total des créations réussies de travaux de mises à jour de représentations.|Aucune dimension|
|jobs.createTwinUpdateJob.failure|Créations des travaux de mises à jour de représentations en échec|Nombre|Total|Total des créations en échec des travaux de mises à jour de représentations.|Aucune dimension|
|jobs.createDirectMethodJob.success|Créations réussies des travaux d’appel de méthode|Nombre|Total|Total des créations réussies des travaux d’appel de méthode directe.|Aucune dimension|
|jobs.createDirectMethodJob.failure|Créations des travaux d’appel de méthode en échec|Nombre|Total|Total des créations en échec des travaux d’appel de méthode directe.|Aucune dimension|
|jobs.listJobs.success|Appels réussis pour répertorier les travaux|Nombre|Total|Total des appels réussis pour répertorier les travaux.|Aucune dimension|
|jobs.listJobs.failure|Appels en échec pour répertorier les travaux|Nombre|Total|Total des appels en échec pour répertorier les travaux.|Aucune dimension|
|jobs.cancelJob.success|Annulations de travaux réussies|Nombre|Total|Total des appels réussis pour annuler un travail.|Aucune dimension|
|jobs.cancelJob.failure|Annulations de travaux en échec|Nombre|Total|Total des appels en échec pour annuler un travail.|Aucune dimension|
|jobs.queryJobs.success|Requêtes de travaux réussies|Nombre|Total|Total des appels réussis pour interroger les travaux.|Aucune dimension|
|jobs.queryJobs.failure|Requêtes de travaux en échec|Nombre|Total|Total des appels en échec pour interroger les travaux.|Aucune dimension|
|jobs.completed|Travaux terminés|Nombre|Total|Total des travaux terminés.|Aucune dimension|
|jobs.failed|Travaux en échec|Nombre|Total|Total des travaux en échec.|Aucune dimension|
|d2c.telemetry.ingress.sendThrottle|Nombre d’erreurs de limitation|Nombre|Total|Nombre d’erreurs de limitation causées par des limitations de débit d’appareil|Aucune dimension|
|dailyMessageQuotaUsed|Nombre total de messages utilisés|Nombre|Moyenne|Nombre total de messages utilisés aujourd’hui. Il s’agit d’une valeur cumulative qui est réinitialisée sur zéro à 00h00 UTC chaque jour.|Aucune dimension|
|deviceDataUsage|Utilisation totale des données d’appareil (déconseillé)|Octets|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|Aucune dimension|
|deviceDataUsageV2|Utilisation totale des données d’appareil (préversion)|Octets|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|Aucune dimension|
|totalDeviceCount|Nombre total d’appareils (préversion)|Nombre|Moyenne|Nombre d’appareils enregistrés sur votre hub IoT|Aucune dimension|
|connectedDeviceCount|Appareils connectés (préversion)|Nombre|Moyenne|Nombre d’appareils connectés à votre hub IoT|Aucune dimension|
|configurations|Métriques de configuration|Nombre|Total|Métriques pour les opérations de configuration|Aucune dimension|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentatives d’enregistrement|Nombre|Total|Nombre d’inscriptions d’appareils tentées|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Appareils attribués|Nombre|Total|Nombre d’appareils affectés à un hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentatives d’attestation|Nombre|Total|Nombre d’attestations d’appareils tentées|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|MetadataRequests|Demandes de métadonnées|Nombre|Nombre|Nombre de demandes de métadonnées. Cosmos DB gère la collection des métadonnées système pour chaque compte, ce qui vous permet d’énumérer les collections, les bases de données, etc., ainsi que leur configuration, et ce gratuitement.|DatabaseName, CollectionName, Region, StatusCode|
|MongoRequestCharge|Frais des requêtes Mongo|Nombre|Total|Unités de requête Mongo consommées|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Requêtes Mongo|Nombre|Nombre|Nombre de requêtes Mongo effectuées|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|TotalRequestUnits|Unités de requête totales|Nombre|Total|Unités de requête consommées|DatabaseName, CollectionName, Region, StatusCode|
|TotalRequests|Total de requêtes|Nombre|Nombre|Nombre de requêtes effectuées|DatabaseName, CollectionName, Region, StatusCode|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Événements publiés|Nombre|Total|Nombre total d’événements publiés dans cette rubrique|Aucune dimension|
|PublishFailCount|Événements ayant échoué|Nombre|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType, Error|
|UnmatchedEventCount|Événements sans correspondance|Nombre|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|Aucune dimension|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|MatchedEventCount|Événements correspondants|Nombre|Total|Nombre total d’événements correspondant à cet abonnement aux événements|Aucune dimension|
|DeliveryAttemptFailCount|Événements d’échec de la remise|Nombre|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error, ErrorType|
|DeliverySuccessCount|Événements remis|Nombre|Total|Nombre total d’événements remis à cet abonnement aux événements|Aucune dimension|
|DestinationProcessingDurationInMs|Durée du traitement de la destination|Millisecondes|Moyenne|Durée du traitement de la destination en millisecondes|Aucune dimension|
|DroppedEventCount|Événements annulés|Nombre|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|Aucune dimension|
|DeadLetteredCount|Événements de lettres mortes|Nombre|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Événements publiés|Nombre|Total|Nombre total d’événements publiés dans cette rubrique|Aucune dimension|
|PublishFailCount|Événements ayant échoué|Nombre|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType, Error|
|UnmatchedEventCount|Événements sans correspondance|Nombre|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|Aucune dimension|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Requêtes ayant réussi (préversion)|Nombre|Total|Requête réussies pour Microsoft.EventHub. (Préversion)|EntityName|
|ServerErrors|Erreurs de serveur. (Préversion)|Nombre|Total|Erreurs de serveur pour Microsoft.EventHub. (Préversion)|EntityName|
|UserErrors|Erreurs d’utilisateur. (Préversion)|Nombre|Total|Erreurs d’utilisateur pour Microsoft.EventHub. (Préversion)|EntityName|
|QuotaExceededErrors|Erreurs de dépassement de quota. (Préversion)|Nombre|Total|Erreurs de dépassement de quota pour Microsoft.EventHub. (Préversion)|EntityName|
|ThrottledRequests|Demandes limitées. (Préversion)|Nombre|Total|Demandes limitées pour Microsoft.EventHub. (Préversion)|EntityName|
|IncomingRequests|Demandes entrantes (préversion)|Nombre|Total|Demandes entrantes pour Microsoft.EventHub. (Préversion)|EntityName|
|IncomingMessages|Messages entrants (préversion)|Nombre|Total|Messages entrants pour Microsoft.EventHub. (Préversion)|EntityName|
|OutgoingMessages|Messages sortants (préversion)|Nombre|Total|Messages sortants pour Microsoft.EventHub. (Préversion)|EntityName|
|IncomingBytes|Octets entrants. (Préversion)|Octets|Total|Octets entrants pour Microsoft.EventHub. (Préversion)|EntityName|
|OutgoingBytes|Octets sortants. (Préversion)|Octets|Total|Octets sortants pour Microsoft.EventHub. (Préversion)|EntityName|
|ActiveConnections|ActiveConnections (préversion)|Nombre|Moyenne|Nombre total de connexions actives pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|ConnectionsOpened|Connexions ouvertes. (Préversion)|Nombre|Moyenne|Connexions ouvertes pour Microsoft.EventHub. (Préversion)|EntityName|
|ConnectionsClosed|Connexions fermées. (Préversion)|Nombre|Moyenne|Connexions fermées pour Microsoft.EventHub. (Préversion)|EntityName|
|CaptureBacklog|Backlog des captures. (Préversion)|Nombre|Total|Backlog des captures de Microsoft.EventHub. (Préversion)|EntityName|
|CapturedMessages|Messages capturés. (Préversion)|Nombre|Total|Messages capturés pour Microsoft.EventHub. (Préversion)|EntityName|
|CapturedBytes|Octets capturés. (Préversion)|Octets|Total|Octets capturés pour Microsoft.EventHub. (Préversion)|EntityName|
|Taille|Taille (Préversion)|Octets|Moyenne|Taille d’un EventHub en octets. (Préversion)|EntityName|
|INREQS|Demandes entrantes|Nombre|Total|Nombre total des demandes d’envoi entrantes pour un espace de noms|Aucune dimension|
|SUCCREQ|Requêtes ayant réussi|Nombre|Total|Nombre total de demandes ayant réussi pour un espace de noms|Aucune dimension|
|FAILREQ|Demandes ayant échoué|Nombre|Total|Nombre total de demandes ayant échoué pour un espace de noms|Aucune dimension|
|SVRBSY|Erreurs de serveur occupé|Nombre|Total|Nombre total d’erreurs de serveur occupé pour un espace de noms|Aucune dimension|
|INTERR|Erreurs internes du serveur|Nombre|Total|Nombre total d’erreurs de serveur internes pour un espace de noms|Aucune dimension|
|MISCERR|Autres erreurs|Nombre|Total|Nombre total de demandes ayant échoué pour un espace de noms|Aucune dimension|
|INMSGS|Messages entrants (déconseillé)|Nombre|Total|Nombre total de messages entrants pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Messages entrants|Aucune dimension|
|EHINMSGS|Messages entrants|Nombre|Total|Nombre total de messages entrants pour un espace de noms|Aucune dimension|
|OUTMSGS|Messages sortants (déconseillé)|Nombre|Total|Nombre total de messages sortants pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Messages sortants|Aucune dimension|
|EHOUTMSGS|Messages sortants|Nombre|Total|Nombre total de messages sortants pour un espace de noms|Aucune dimension|
|EHINMBS|Octets entrants (déconseillé)|Octets|Total|Débit de messages entrants Event Hub pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Octets entrants|Aucune dimension|
|EHINBYTES|Octets entrants|Octets|Total|Débit de messages entrants Event Hub pour un espace de noms|Aucune dimension|
|EHOUTMBS|Octets sortants (déconseillé)|Octets|Total|Débit de messages sortants Event Hub pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Octets sortants|Aucune dimension|
|EHOUTBYTES|Octets sortants|Octets|Total|Débit de messages sortants Event Hub pour un espace de noms|Aucune dimension|
|EHABL|Archiver les messages de file d’attente|Nombre|Total|Messages d’archive Event Hub dans le backlog pour un espace de noms|Aucune dimension|
|EHAMSGS|Archiver les messages|Nombre|Total|Messages archivés Event Hub dans un espace de noms|Aucune dimension|
|EHAMBS|Débit message archive|Octets|Total|Débit de messages archivés Event Hub dans un espace de noms|Aucune dimension|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Requêtes ayant réussi (préversion)|Nombre|Total|Requête réussies pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|ServerErrors|Erreurs de serveur. (Préversion)|Nombre|Total|Erreurs de serveur pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|UserErrors|Erreurs d’utilisateur. (Préversion)|Nombre|Total|Erreurs d’utilisateur pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|QuotaExceededErrors|Erreurs de dépassement de quota. (Préversion)|Nombre|Total|Erreurs de dépassement de quota pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|ThrottledRequests|Demandes limitées. (Préversion)|Nombre|Total|Demandes limitées pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|IncomingRequests|Demandes entrantes (préversion)|Nombre|Total|Demandes entrantes pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|IncomingMessages|Messages entrants (préversion)|Nombre|Total|Messages entrants pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|OutgoingMessages|Messages sortants (préversion)|Nombre|Total|Messages sortants pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|IncomingBytes|Octets entrants. (Préversion)|Octets|Total|Octets entrants pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|OutgoingBytes|Octets sortants. (Préversion)|Octets|Total|Octets sortants pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|ActiveConnections|ActiveConnections (préversion)|Nombre|Moyenne|Nombre total de connexions actives pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|ConnectionsOpened|Connexions ouvertes. (Préversion)|Nombre|Moyenne|Connexions ouvertes pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|ConnectionsClosed|Connexions fermées. (Préversion)|Nombre|Moyenne|Connexions fermées pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|CaptureBacklog|Backlog des captures. (Préversion)|Nombre|Total|Backlog des captures de Microsoft.EventHub. (Préversion)|Aucune dimension|
|CapturedMessages|Messages capturés. (Préversion)|Nombre|Total|Messages capturés pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|CapturedBytes|Octets capturés. (Préversion)|Octets|Total|Octets capturés pour Microsoft.EventHub. (Préversion)|Aucune dimension|
|UC|UC (Préversion)|Pourcentage|Maximale|Utilisation de l’UC en pourcentage du cluster Event Hub|Rôle|
|AvailableMemory|Mémoire disponible (Préversion)|Nombre|Maximale|Mémoire disponible en octets pour le cluster Event Hub|Rôle|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|GatewayRequests|Demandes de la passerelle|Nombre|Total|Nombre de demandes de la passerelle|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Demandes de la passerelle par catégorie|Nombre|Total|Nombre de demandes de la passerelle par catégories (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ObservedMetricValue|Valeur de métrique observée|Nombre|Moyenne|Valeur calculée par mise à l’échelle automatique lors de l’exécution|MetricTriggerSource|
|MetricThreshold|Seuil de métrique|Nombre|Moyenne|Seuil de mise à l’échelle automatique configurée lors de l’exécution de la mise à l’échelle automatique.|MetricTriggerRule|
|ObservedCapacity|Capacité observée|Nombre|Moyenne|Capacité envoyée à la mise à l’échelle automatique lors de l’exécution.|Aucune dimension|
|ScaleActionsInitiated|Actions de mise à l’échelle initiées|Nombre|Total|Direction de l’opération de mise à l’échelle.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components
(Préversion publique)

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|availabilityResults/duration|Durée du test|Millisecondes|Moyenne|Durée du test|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|billingMeters/telemetryCount|Nombre de points de données|Nombre|Total|Nombre de points de données envoyés à cette ressource Application Insights. Cette métrique est traitée avec une latence pouvant atteindre deux heures.|billing/telemetryItemType, billing/telemetryItemSource|
|billingMeters/telemetrySize|Volume de point de données|Octets|Total|Volume de données envoyé à cette ressource Application Insights. Cette métrique est traitée avec une latence pouvant atteindre deux heures.|billing/telemetryItemType, billing/telemetryItemSource|
|browserTimings/networkDuration|Temps de connexion au réseau pour le chargement de page|Millisecondes|Moyenne|Temps écoulé entre la requête de l’utilisateur et la connexion réseau. Inclut la connexion de recherche DNS et de transport.|Aucune dimension|
|browserTimings/processingDuration|Temps de traitement du client|Millisecondes|Moyenne|Temps écoulé entre la réception du dernier octet d’un document et le chargement du modèle DOM. Les demandes asynchrones peuvent encore être en cours de traitement.|Aucune dimension|
|browserTimings/receiveDuration|Temps de réception de réponse|Millisecondes|Moyenne|Temps écoulé entre le premier et le dernier octet, ou jusqu’à la déconnexion.|Aucune dimension|
|browserTimings/sendDuration|Temps d’envoi de demande|Millisecondes|Moyenne|Temps écoulé entre la connexion réseau et la réception du premier octet.|Aucune dimension|
|browserTimings/totalDuration|Temps de chargement de la page de navigateur|Millisecondes|Moyenne|Temps s’écoulant entre la demande de l’utilisateur et le chargement du DOM, des feuilles de style, des scripts et des images.|Aucune dimension|
|dependencies/count|Appels de dépendance|Nombre|Nombre|Nombre d’appels effectués par l’application à des ressources externes.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Durée de la dépendance|Millisecondes|Moyenne|Durée des appels effectués par l’application aux ressources externes.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Défaillances de dépendance|Nombre|Nombre|Nombre d’échecs des appels de dépendance effectués par l’application à des ressources externes.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Affichages de page|Nombre|Nombre|Nombre de pages consultées.|operation/synthetic|
|pageViews/duration|Temps de chargement de la page consultée|Millisecondes|Moyenne|Temps de chargement de la page consultée|operation/synthetic|
|performanceCounters/requestExecutionTime|Durée d’exécution de la requête HTTP|Millisecondes|Moyenne|Durée d’exécution de la demande la plus récente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Requêtes HTTP dans la file d’attente de l’application|Nombre|Moyenne|Longueur de la file d'attente des demandes d'application.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Taux de requêtes HTTP|CountPerSecond|Moyenne|Taux par seconde de l'ensemble des demandes à l'application provenant d'ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Taux d’exceptions|CountPerSecond|Moyenne|Nombre d’exceptions prises en charge et non prises en charge signalées à Windows, notamment les exceptions .NET et les exceptions non managées converties en exceptions .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Taux d’E/S du processus|BytesPerSecond|Moyenne|Nombre total d’octets par seconde lus et écrits sur des fichiers, un réseau et des appareils.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Processeur de processus|Pourcentage|Moyenne|Temps écoulé en pourcentage que tous les threads de processus a passé à utiliser le processeur pour exécuter des instructions. Il peut varier de 0 à 100. Cette métrique indique les performances du processus w3wp uniquement.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Temps processeur|Pourcentage|Moyenne|Temps en pourcentage que le processus a passé dans des threads actifs.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Mémoire disponible|Octets|Moyenne|Mémoire physique prête à être allouée immédiatement pour un processus ou pour le système.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Octets privés du processus|Octets|Moyenne|Mémoire allouée exclusivement aux processus de l’application analysée.|cloud/roleInstance|
|requests/duration|Temps de réponse du serveur|Millisecondes|Moyenne|Temps écoulé entre la réception d'une requête HTTP et la fin de l'envoi de la réponse.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Requêtes serveur|Nombre|Nombre|Nombre de requêtes HTTP terminées.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Demandes ayant échoué|Nombre|Nombre|Nombre de requêtes HTTP marquées comme ayant échoué. Dans la plupart des cas, il s’agit de requêtes avec un code de réponse >= 400 et différent de 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|exceptions/count|Exceptions|Nombre|Total|Nombre combiné de toutes les exceptions non interceptées.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Exceptions du navigateur|Nombre|Total|Nombre d’exceptions non interceptées levées dans le navigateur.|Aucune dimension|
|exceptions/server|Exceptions de serveur|Nombre|Total|Nombre d’exceptions non interceptées levées dans l’application serveur.|cloud/roleName, cloud/roleInstance|
|traces/count|Nombre de traces|Nombre|Total|Nombre de documents de traces|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ServiceApiHit|Correspondances totales de l'API de service|Nombre|Nombre|Nombre total de correspondances de l'API de service|ActivityType, ActivityName|
|ServiceApiLatency|Latence globale de l'API de service|Millisecondes|Moyenne|Latence globale des demandes de l'API de service|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Résultats totaux de l'API de service|Nombre|Nombre|Nombre total de résultats de l'API de service|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ClusterDataCapacityFactor|Utilisation du cache|Pourcentage|Moyenne|Niveau d’utilisation dans l’étendue du cluster|Aucune dimension|
|QueryDuration|Durée de la requête|Millisecondes|Moyenne|Durée des requêtes en secondes|QueryStatus|
|IngestionsLoadFactor|Utilisation de l’ingestion|Pourcentage|Moyenne|Ratio d’emplacements d’ingestion utilisés dans le cluster|Aucune dimension|
|IsEngineAnsweringQuery|Connexion persistante|Nombre|Moyenne|Contrôle d’intégrité indiquant que le cluster répond aux requêtes|Aucune dimension|
|IngestCommandOriginalSizeInMb|Volume d’ingestion (en Mo)|Nombre|Total|Volume total de données ingérées dans le cluster (en Mo)|Aucune dimension|
|EventAgeSeconds|Latence d’ingestion (en secondes)|Secondes|Moyenne|Durée d’ingestion entre la source (par ex., message dans Event Hub) et le cluster en secondes|Aucune dimension|
|EventReceivedFromEventHub|Événements traités (pour Event Hubs)|Nombre|Total|Nombre d’événements traités par le cluster lors de l’ingestion à partir d’Event Hub|Aucune dimension|
|IngestionResult|Résultat de l’ingestion|Nombre|Nombre|Nombre d’opérations d’ingestion|IngestionResultDetails|
|EngineCPU|UC|Pourcentage|Moyenne|Niveau d’utilisation de l’UC|Aucune dimension|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Usage|Usage|Nombre|Nombre|Nombre d’appels d’API|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|Exécutions démarrées|Nombre|Total|Nombre d’exécutions de flux de travail démarrées.|Aucune dimension|
|RunsCompleted|Exécutions terminées|Nombre|Total|Nombre d’exécutions de flux de travail terminées.|Aucune dimension|
|RunsSucceeded|Exécutions réussies|Nombre|Total|Nombre d’exécutions de flux de travail ayant réussi.|Aucune dimension|
|RunsFailed|Exécutions ayant échoué|Nombre|Total|Nombre d’exécutions de flux de travail ayant échoué.|Aucune dimension|
|RunsCancelled|Exécutions annulées|Nombre|Total|Nombre d’exécutions de flux de travail annulées.|Aucune dimension|
|RunLatency|Latence d’exécution|Secondes|Moyenne|Latence des exécutions de flux de travail terminées.|Aucune dimension|
|RunSuccessLatency|Latence d’exécution ayant réussi|Secondes|Moyenne|Latence des exécutions de flux de travail ayant réussi.|Aucune dimension|
|RunThrottledEvents|Exécuter événements limités|Nombre|Total|Nombre d’actions de flux de travail ou d’événements limités par déclencheur.|Aucune dimension|
|RunFailurePercentage|Pourcentage d’échec des exécutions|Pourcentage|Total|Pourcentage d’exécutions de flux de travail ayant échoué.|Aucune dimension|
|ActionsStarted|Actions démarrées |Nombre|Total|Nombre d’actions de flux de travail démarrées.|Aucune dimension|
|ActionsCompleted|Actions terminées |Nombre|Total|Nombre d’actions de flux de travail terminées.|Aucune dimension|
|ActionsSucceeded|Actions ayant réussi |Nombre|Total|Nombre d’actions de flux de travail ayant réussi.|Aucune dimension|
|ActionsFailed|Actions ayant échoué|Nombre|Total|Nombre d’actions de flux de travail ayant échoué.|Aucune dimension|
|ActionsSkipped|Actions ignorées |Nombre|Total|Nombre d’actions de flux de travail ignorées.|Aucune dimension|
|ActionLatency|Latence de l’action |Secondes|Moyenne|Latence des actions de flux de travail terminés.|Aucune dimension|
|ActionSuccessLatency|Latence de réussite d’action |Secondes|Moyenne|Latence des actions de flux de travail ayant réussi.|Aucune dimension|
|ActionThrottledEvents|Événements d’action limitée|Nombre|Total|Nombre d’événements limités d’action de flux de travail.|Aucune dimension|
|TriggersStarted|Déclenchements lancés |Nombre|Total|Nombre de déclencheurs de flux de travail démarrés.|Aucune dimension|
|TriggersCompleted|Déclenchements terminés |Nombre|Total|Nombre de déclencheurs de flux de travail terminés.|Aucune dimension|
|TriggersSucceeded|Déclenchements ayant réussi |Nombre|Total|Nombre de déclencheurs de flux de travail ayant réussi.|Aucune dimension|
|TriggersFailed|Déclenchements ayant échoué |Nombre|Total|Nombre de déclencheurs de flux de travail ayant échoué.|Aucune dimension|
|TriggersSkipped|Déclenchements ignorés|Nombre|Total|Nombre de déclencheurs de flux de travail ignorés.|Aucune dimension|
|TriggersFired|Déclenchements activés |Nombre|Total|Nombre de déclencheurs de flux de travail activés.|Aucune dimension|
|TriggerLatency|Latence de déclencheur |Secondes|Moyenne|Latence des déclenchements de flux de travail terminés.|Aucune dimension|
|TriggerFireLatency|Latence d’activation de déclencheur |Secondes|Moyenne|Latence des déclencheurs de flux de travail activés.|Aucune dimension|
|TriggerSuccessLatency|Latence déclencheur ayant réussi |Secondes|Moyenne|Latence des déclencheurs de flux de travail ayant réussi.|Aucune dimension|
|TriggerThrottledEvents|Événements limités par déclencheur|Nombre|Total|Nombre d’événements de flux de travail limités par déclencheur.|Aucune dimension|
|BillableActionExecutions|Exécutions d’actions facturables|Nombre|Total|Nombre d’exécutions d’actions de flux de travail facturées.|Aucune dimension|
|BillableTriggerExecutions|Exécutions de déclencheurs facturables|Nombre|Total|Nombre d’exécutions de déclencheurs de flux de travail facturées.|Aucune dimension|
|TotalBillableExecutions|Nombre total d’exécutions facturables|Nombre|Total|Nombre d’exécutions de flux de travail facturées.|Aucune dimension|
|BillingUsageNativeOperation|Utilisation de la facturation pour les exécutions d’opérations natives|Nombre|Total|Nombre d’exécutions d’opérations natives facturées.|Aucune dimension|
|BillingUsageStandardConnector|Utilisation de la facturation pour les exécutions de connecteurs Standard|Nombre|Total|Nombre d’exécutions de connecteurs Standard facturées.|Aucune dimension|
|BillingUsageStorageConsumption|Utilisation de la facturation pour les exécutions de consommation du stockage|Nombre|Total|Nombre d’exécutions de consommation du stockage facturées.|Aucune dimension|
|BillingUsageNativeOperation|Utilisation de la facturation pour les exécutions d’opérations natives|Nombre|Total|Nombre d’exécutions d’opérations natives facturées.|Aucune dimension|
|BillingUsageStandardConnector|Utilisation de la facturation pour les exécutions de connecteurs Standard|Nombre|Total|Nombre d’exécutions de connecteurs Standard facturées.|Aucune dimension|
|BillingUsageStorageConsumption|Utilisation de la facturation pour les exécutions de consommation du stockage|Nombre|Total|Nombre d’exécutions de consommation du stockage facturées.|Aucune dimension|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|AverageOtherLatency|Autre latence moyenne|ms/op|Moyenne|Autre latence moyenne (qui n’est pas en lecture ou écriture) en millisecondes par opération|Aucune dimension|
|AverageReadLatency|Latence de lecture moyenne|ms/op|Moyenne|Latence de lecture moyenne en millisecondes par opération|Aucune dimension|
|AverageTotalLatency|Latence totale moyenne|ms/op|Moyenne|Latence totale moyenne en millisecondes par opération|Aucune dimension|
|AverageWriteLatency|Latence d’écriture moyenne|ms/op|Moyenne|Latence d’écriture moyenne en millisecondes par opération|Aucune dimension|
|FilesystemOtherOps|Autres opérations du système de fichiers|ops|Moyenne|Nombre d’autres opérations du système de fichiers (qui ne sont pas en lecture ou écriture)|Aucune dimension|
|FilesystemReadOps|Opérations de lecture du système de fichiers|ops|Moyenne|Nombre d’opérations de lecture du système de fichiers|Aucune dimension|
|FilesystemTotalOps|Nombre total d’opérations du système de fichiers|ops|Moyenne|Somme de toutes les opérations du système de fichiers|Aucune dimension|
|FilesystemWriteOps|Opérations d’écriture du système de fichiers|ops|Moyenne|Nombre d’opérations d’écriture du système de fichiers|Aucune dimension|
|IoBytesPerOtherOps|Octets d’E/S par autres opérations|octets/op|Moyenne|Nombre d’octets d’E/S par autres opérations (qui ne sont pas en lecture ou écriture)|Aucune dimension|
|IoBytesPerReadOps|Octets d’E/S par opérations de lecture|octets/op|Moyenne|Nombre d’octets d’E/S par opération de lecture|Aucune dimension|
|IoBytesPerTotalOps|Octets d’E/S par opération pour toutes les opérations|octets/op|Moyenne|Somme de tous les octets d’E/S d’opération|Aucune dimension|
|IoBytesPerWriteOps|Octets d’E/S par opérations d’écriture|octets/op|Moyenne|Nombre d’octets d’E/S par opération d’écriture|Aucune dimension|
|OtherIops|Autres E/S par seconde|opérations/seconde|Moyenne|Autre opération d’E/S par seconde|Aucune dimension|
|OtherThroughput|Autre débit|Mbits/s|Moyenne|Autre débit (qui n’est pas en lecture ou écriture) en mégaoctets par seconde|Aucune dimension|
|ReadIops|E/S par seconde en lecture|opérations/seconde|Moyenne|Opérations d’E/S en lecture par seconde|Aucune dimension|
|ReadThroughput|Débit de lecture|Mbits/s|Moyenne|Débit de lecture en mégaoctets par seconde|Aucune dimension|
|TotalIops|Nombre total d’E/S par seconde|opérations/seconde|Moyenne|Somme de toutes opérations d’E/S en lecture par seconde|Aucune dimension|
|TotalThroughput|Débit total|Mbits/s|Moyenne|Somme de tout le débit en mégaoctets par seconde|Aucune dimension|
|VolumeAllocatedSize|Taille allouée de volume|octets|Moyenne|Taille allouée du volume (pas les octets utilisés réels)|Aucune dimension|
|VolumeLogicalSize|Taille logique du volume|octets|Moyenne|Taille logique du volume (octets utilisés)|Aucune dimension|
|VolumeSnapshotSize|Taille d’instantané du volume|octets|Moyenne|Taille de tous les instantanés dans le volume|Aucune dimension|
|WriteIops|E/S par seconde en écriture|opérations/seconde|Moyenne|Opérations d’E/S en écriture par seconde|Aucune dimension|
|WriteThroughput|Débit d’écriture|Mbits/s|Moyenne|Débit d’écriture en mégaoctets par seconde|Aucune dimension|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Taille allouée du pool de volumes|octets|Moyenne|Taille allouée du pool (pas les octets utilisés réels)|Aucune dimension|
|VolumePoolAllocatedUsed|Taille allouée utilisée du pool de volumes|octets|Moyenne|Taille allouée utilisée du pool|Aucune dimension|
|VolumePoolTotalLogicalSize|Taille logique totale du pool de volumes|octets|Moyenne|Somme de la taille logique de tous les volumes appartenant au pool|Aucune dimension|
|VolumePoolTotalSnapshotSize|Taille d’instantané totale du pool de volumes|octets|Moyenne|Somme de tous les instantanés dans le pool|Aucune dimension|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BytesSentRate|Octets envoyés|Nombre|Total|Nombre d’octets envoyés par l’interface réseau|Aucune dimension|
|BytesReceivedRate|Octets reçus|Nombre|Total|Nombre d’octets reçus par l’interface réseau|Aucune dimension|
|PacketsSentRate|Paquets envoyés|Nombre|Total|Nombre de paquets envoyés par l’interface réseau|Aucune dimension|
|PacketsReceivedRate|Paquets reçus|Nombre|Total|Nombre de paquets reçus par l’interface réseau|Aucune dimension|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|VipAvailability|Disponibilité du chemin d’accès aux données|Nombre|Moyenne|Disponibilité du chemin d’accès aux données d’équilibreur de charge moyenne par durée|FrontendIPAddress, FrontendPort|
|DipAvailability|État de la sonde d’intégrité|Nombre|Moyenne|État de la sonde d’intégrité d’équilibreur de charge moyen par durée|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Nombre d’octets|Nombre|Total|Nombre total d’octets transmis dans une période de temps|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Nombre de paquets|Nombre|Total|Nombre total de paquets transmis dans une période de temps|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|Nombre de SYN|Nombre|Total|Nombre total de paquets SYN transmis dans une période de temps|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Nombre de connexions SNAT|Nombre|Total|Nombre total de connexions SNAT créées dans une période de temps|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Ports SNAT alloués (Préversion)|Nombre|Total|Nombre total de ports SNAT alloués pendant la période|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Ports SNAT utilisés (Préversion)|Nombre|Total|Nombre total de ports SNAT utilisés pendant une période|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QueryVolume|Volume de requête|Nombre|Total|Nombre de requêtes servies pour une zone DNS|Aucune dimension|
|RecordSetCount|Nombre de jeux d’enregistrements|Nombre|Maximale|Nombre de jeux d’enregistrements dans une zone DNS|Aucune dimension|
|RecordSetCapacityUtilization|Utilisation de la capacité du jeu d’enregistrements|Pourcentage|Maximale|Pourcentage de la capacité du jeu d’enregistrements utilisée par une zone DNS|Aucune dimension|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PacketsInDDoS|Paquets entrants DDoS|CountPerSecond|Maximale|Paquets entrants DDoS|Aucune dimension|
|PacketsDroppedDDoS|Paquets entrants ignorés DDoS|CountPerSecond|Maximale|Paquets entrants ignorés DDoS|Aucune dimension|
|PacketsForwardedDDoS|Paquets entrants transférés DDoS|CountPerSecond|Maximale|Paquets entrants transférés DDoS|Aucune dimension|
|TCPPacketsInDDoS|Paquets TCP entrants DDoS|CountPerSecond|Maximale|Paquets TCP entrants DDoS|Aucune dimension|
|TCPPacketsDroppedDDoS|Paquets TCP entrants TCP ignorés DDoS|CountPerSecond|Maximale|Paquets TCP entrants TCP ignorés DDoS|Aucune dimension|
|TCPPacketsForwardedDDoS|Paquets TCP entrants transférés DDoS|CountPerSecond|Maximale|Paquets TCP entrants transférés DDoS|Aucune dimension|
|UDPPacketsInDDoS|Paquets UDP entrants DDoS|CountPerSecond|Maximale|Paquets UDP entrants DDoS|Aucune dimension|
|UDPPacketsDroppedDDoS|Paquets UDP entrants ignorés DDoS|CountPerSecond|Maximale|Paquets UDP entrants ignorés DDoS|Aucune dimension|
|UDPPacketsForwardedDDoS|Paquets UDP entrants transférés DDoS|CountPerSecond|Maximale|Paquets UDP entrants transférés DDoS|Aucune dimension|
|BytesInDDoS|Octets entrants DDoS|BytesPerSecond|Maximale|Octets entrants DDoS|Aucune dimension|
|BytesDroppedDDoS|Octets entrants supprimés DDoS|BytesPerSecond|Maximale|Octets entrants supprimés DDoS|Aucune dimension|
|BytesForwardedDDoS|Octets entrants transférés DDoS|BytesPerSecond|Maximale|Octets entrants transférés DDoS|Aucune dimension|
|TCPBytesInDDoS|Octets TCP entrants DDoS|BytesPerSecond|Maximale|Octets TCP entrants DDoS|Aucune dimension|
|TCPBytesDroppedDDoS|Octets TCP entrants supprimés DDoS|BytesPerSecond|Maximale|Octets TCP entrants supprimés DDoS|Aucune dimension|
|TCPBytesForwardedDDoS|Octets TCP entrants transférés DDoS|BytesPerSecond|Maximale|Octets TCP entrants transférés DDoS|Aucune dimension|
|UDPBytesInDDoS|Octets UDP entrants DDoS|BytesPerSecond|Maximale|Octets UDP entrants DDoS|Aucune dimension|
|UDPBytesDroppedDDoS|Octets UDP entrants supprimés DDoS|BytesPerSecond|Maximale|Octets UDP entrants supprimés DDoS|Aucune dimension|
|UDPBytesForwardedDDoS|Octets UDP entrants transférés DDoS|BytesPerSecond|Maximale|Octets UDP entrants transférés DDoS|Aucune dimension|
|IfUnderDDoSAttack|Sous attaque DDoS ou non|Nombre|Maximale|Sous attaque DDoS ou non|Aucune dimension|
|DDoSTriggerTCPPackets|Paquets TCP entrants pour déclencher l’atténuation DDoS|CountPerSecond|Maximale|Paquets TCP entrants pour déclencher l’atténuation DDoS|Aucune dimension|
|DDoSTriggerUDPPackets|Paquets UDP entrants pour déclencher l’atténuation DDoS|CountPerSecond|Maximale|Paquets UDP entrants pour déclencher l’atténuation DDoS|Aucune dimension|
|DDoSTriggerSYNPackets|Paquets SYN entrants pour déclencher l’atténuation DDoS|CountPerSecond|Maximale|Paquets SYN entrants pour déclencher l’atténuation DDoS|Aucune dimension|
|VipAvailability|Disponibilité du chemin d’accès aux données|Nombre|Moyenne|Disponibilité d’adresse IP moyenne par durée|Port|
|ByteCount|Nombre d’octets|Nombre|Total|Nombre total d’octets transmis dans une période de temps|Port, Direction|
|PacketCount|Nombre de paquets|Nombre|Total|Nombre total de paquets transmis dans une période de temps|Port, Direction|
|SynCount|Nombre de SYN|Nombre|Total|Nombre total de paquets SYN transmis dans une période de temps|Port, Direction|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Throughput|Débit|BytesPerSecond|Total|Nombre d’octets par seconde servis par Application Gateway|Aucune dimension|
|UnhealthyHostCount|Nombre d’hôtes non intègres|Nombre|Moyenne|Nombre d’hôtes principaux non intègres|BackendSettingsPool|
|HealthyHostCount|Nombre d’hôtes intègres|Nombre|Moyenne|Nombre d’hôtes principaux intègres|BackendSettingsPool|
|TotalRequests|Total de requêtes|Nombre|Total|Nombre de requêtes réussies servies par Application Gateway|BackendSettingsPool|
|FailedRequests|Demandes ayant échoué|Nombre|Total|Nombre de requêtes en échec servies par Application Gateway|BackendSettingsPool|
|ResponseStatus|État de la réponse|Nombre|Total|État de la réponse HTTP retourné par Application Gateway|HttpStatusGroup|
|CurrentConnections|Connexions courantes|Nombre|Total|Nombre de connexions courantes établies avec Application Gateway|Aucune dimension|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|AverageBandwidth|Bande passante S2S de passerelle|BytesPerSecond|Moyenne|Bande passante site à site moyenne d’une passerelle en octets par seconde|Aucune dimension|
|P2SBandwidth|Bande passante P2S de passerelle|BytesPerSecond|Moyenne|Bande passante point à site moyenne d’une passerelle en octets par seconde|Aucune dimension|
|P2SConnectionCount|Nombre de connexions P2S|Nombre|Maximale|Nombre de connexions point à site d’une passerelle|Protocole|
|TunnelAverageBandwidth|Bande passante de tunnel|BytesPerSecond|Moyenne|Bande passante moyenne d’un tunnel en octets par seconde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Octets de sortie de tunnel|Octets|Total|Octets sortants d’un tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Octets d’entrée de tunnel|Octets|Total|Octets entrants d’un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Paquets de sortie de tunnel|Nombre|Total|Nombre de paquets sortants d’un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Paquets en entrée de tunnel|Nombre|Total|Nombre de paquets entrants d’un tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Rejet de paquets TS de sortie de tunnel pour incompatibilité|Nombre|Total|Nombre de rejets de paquets sortants du sélecteur de trafic pour incompatibilité dans un tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Rejet de paquets TS d’entrée de tunnel pour incompatibilité|Nombre|Total|Nombre de rejets de paquets entrants du sélecteur de trafic pour incompatibilité dans un tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Moyenne|Bits entrant dans Azure par seconde|Aucune dimension|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Moyenne|Bits sortant d’Azure par seconde|Aucune dimension|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Moyenne|Bits entrant dans Azure par seconde|Aucune dimension|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Moyenne|Bits sortant d’Azure par seconde|Aucune dimension|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Moyenne|Bits entrant dans Azure par seconde|Aucune dimension|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Moyenne|Bits sortant d’Azure par seconde|Aucune dimension|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QpsByEndpoint|Requêtes par point de terminaison renvoyé|Nombre|Total|Nombre de fois où un point de terminaison Traffic Manager a été renvoyé dans le laps de temps donné|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|État du point de terminaison par point de terminaison|Nombre|Maximale|1 si l’état de sondage d’un point de terminaison est « activé », 0 dans le cas contraire.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ProbesFailedPercent|% de sondes ayant échoué|Pourcentage|Moyenne|% de sondes de surveillance de connectivité ayant échoué|Aucune dimension|
|AverageRoundtripMs|Avg. Durée aller-retour (ms)|Millisecondes|Moyenne|Durée aller-retour réseau moyenne (ms) pour les sondes de surveillance de connectivité envoyées entre la source et la destination|Aucune dimension|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RequestCount|Nombre de requêtes|Nombre|Total|Nombre de requêtes clients prises en charge par le proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Taille de la requête|Octets|Total|Nombre d’octets envoyés en tant que requêtes de clients au proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Taille de la réponse|Octets|Total|Nombre d’octets envoyés en tant que réponses du proxy HTTP/S aux clients|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Nombre de requêtes de backend|Nombre|Total|Nombre de requêtes envoyées du proxy HTTP/S aux serveurs principaux|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Latence de requête du backend|Millisecondes|Moyenne|Temps calculé à partir du moment où la requête est envoyée par le proxy HTTP/S au serveur principal jusqu’à ce que le proxy HTTP/S reçoive le dernier octet de la réponse du serveur principal|Backend|
|TotalLatency|Latence totale|Millisecondes|Moyenne|Temps calculé à partir du moment où la requête du client est reçue par le proxy HTTP/S jusqu’à ce que le client accuse réception du dernier octet de la réponse du proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Pourcentage d’intégrité du backend|Pourcentage|Moyenne|Pourcentage de sondes d’intégrité réussies du proxy HTTP/S vers les serveurs principaux|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Nombre de requêtes du pare-feu d’applications web|Nombre|Total|Nombre de requêtes clients traitées par le pare-feu d’applications web|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|registration.all|Opérations d’inscription|Nombre|Total|Total des opérations d’inscription réussies (requêtes de mises à jour de créations et suppressions). |Aucune dimension|
|registration.create|Opérations de création d’inscription|Nombre|Total|Total des créations d’inscription réussies.|Aucune dimension|
|registration.update|Opérations de mise à jour d’inscription|Nombre|Total|Total des mises à jour d’inscription réussies.|Aucune dimension|
|registration.get|Opérations de lecture d’inscription|Nombre|Total|Total des requêtes d’inscription réussies.|Aucune dimension|
|registration.delete|Opérations de suppression d’inscription|Nombre|Total|Total des suppressions d’inscription réussies.|Aucune dimension|
|incoming|Messages entrants|Nombre|Total|Total des appels d’API d’envoi réussis. |Aucune dimension|
|incoming.scheduled|Notifications Push planifiées envoyées|Nombre|Total|Notifications Push planifiées annulées|Aucune dimension|
|incoming.scheduled.cancel|Notifications Push planifiées annulées|Nombre|Total|Notifications Push planifiées annulées|Aucune dimension|
|scheduled.pending|Notifications planifiées en attente|Nombre|Total|Notifications planifiées en attente|Aucune dimension|
|installation.all|Opérations de gestion de l’installation|Nombre|Total|Opérations de gestion de l’installation|Aucune dimension|
|installation.get|Opérations d’obtention d’installation|Nombre|Total|Opérations d’obtention d’installation|Aucune dimension|
|installation.upsert|Opérations de création ou de mise à jour d’installation|Nombre|Total|Opérations de création ou de mise à jour d’installation|Aucune dimension|
|installation.patch|Opérations d’installation de correctif|Nombre|Total|Opérations d’installation de correctif|Aucune dimension|
|installation.delete|Opérations de suppression d’installation|Nombre|Total|Opérations de suppression d’installation|Aucune dimension|
|outgoing.allpns.success|Notifications réussies|Nombre|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.allpns.invalidpayload|Erreurs de charge utile|Nombre|Total|Nombre d’opérations Push en échec, car le PNS a retourné une erreur de charge utile incorrecte.|Aucune dimension|
|outgoing.allpns.pnserror|Erreurs système de notification externe|Nombre|Total|Nombre d’opérations Push en échec en raison d’un problème de communication avec le PNS (à l’exclusion des problèmes d’authentification).|Aucune dimension|
|outgoing.allpns.channelerror|Erreurs de canal|Nombre|Total|Nombre d’opérations Push en échec, car le canal n’était pas valide ni associé à l’application appropriée, limité ou arrivé à expiration.|Aucune dimension|
|outgoing.allpns.badorexpiredchannel|Erreurs de canal incorrect ou arrivé à expiration|Nombre|Total|Nombre d’opérations Push en échec, car le canal/jeton/registrationId indiqué dans l’inscription est arrivé à expiration ou non valide.|Aucune dimension|
|outgoing.wns.success|Notifications WNS réussies|Nombre|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.wns.invalidcredentials|Erreurs d’autorisation WNS (informations d’identification non valides)|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées. (Windows Live ne reconnaît pas les informations d’identification).|Aucune dimension|
|outgoing.wns.badchannel|Erreur de canal WNS incorrect|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément ChannelURI indiqué dans l'inscription n'a pas été reconnu (état WNS : 404 - Introuvable).|Aucune dimension|
|outgoing.wns.expiredchannel|Erreur de canal WNS arrivé à expiration|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément ChannelURI est arrivé à expiration (état WNS : 410 Supprimé).|Aucune dimension|
|outgoing.wns.throttled|Notifications WNS limitées|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car WNS limite cette application (état WNS : 406 Non acceptable).|Aucune dimension|
|outgoing.wns.tokenproviderunreachable|Erreurs d’autorisation WNS (inaccessible)|Nombre|Total|Windows Live n’est pas accessible.|Aucune dimension|
|outgoing.wns.invalidtoken|Erreurs d’autorisation WNS (jeton non valide)|Nombre|Total|Le jeton fourni à WNS n'est pas valide (état WNS : 401 - Non autorisé).|Aucune dimension|
|outgoing.wns.wrongtoken|Erreurs d’autorisation WNS (jeton incorrect)|Nombre|Total|Le jeton fourni à WNS est valide mais il concerne une autre application (état WNS : 403 - Interdit). Cela peut se produire si l’élément ChannelURI indiqué dans l’inscription est associé à une autre application. Vérifiez que l’application cliente est associée à l’application dont les informations d’identification figurent dans le hub de notification.|Aucune dimension|
|outgoing.wns.invalidnotificationformat|Format de notification WNS non valide|Nombre|Total|Le format de la notification n'est pas valide (état WNS : 400). Notez que WNS ne rejette pas toutes les charges utiles non valides.|Aucune dimension|
|outgoing.wns.invalidnotificationsize|Erreur de taille de notification WNS non valide|Nombre|Total|La charge utile de notification est trop grande (état WNS : 413).|Aucune dimension|
|outgoing.wns.channelthrottled|Canal WNS limité|Nombre|Total|La notification a été supprimée car l'élément ChannelURI contenu dans l'inscription est limité (en-tête de réponse WNS : X-WNS-NotificationStatus:channelThrottled).|Aucune dimension|
|outgoing.wns.channeldisconnected|Canal WNS déconnecté|Nombre|Total|La notification a été supprimée car l'élément ChannelURI contenu dans l'inscription est limité (en-tête de réponse WNS : X-WNS-DeviceConnectionStatus: déconnecté).|Aucune dimension|
|outgoing.wns.dropped|Notifications WNS supprimées|Nombre|Total|La notification a été supprimée, car l’élément ChannelURI indiqué dans l’inscription est limité (X-WNS-NotificationStatus : supprimé, mais pas X-WNS-DeviceConnectionStatus : déconnecté).|Aucune dimension|
|outgoing.wns.pnserror|Erreurs WNS|Nombre|Total|La notification n’a pas été remise en raison d’erreurs de communication avec WNS.|Aucune dimension|
|outgoing.wns.authenticationerror|Erreurs d’authentification WNS|Nombre|Total|La notification n’a pas été remise en raison d’erreurs de communication avec Windows Live, d’informations d’identification non valides ou d’un jeton incorrect.|Aucune dimension|
|outgoing.apns.success|Notifications APNS réussies|Nombre|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.apns.invalidcredentials|Erreurs d’autorisation APNS|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|outgoing.apns.badchannel|Erreur de canal APNS incorrect|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car le jeton n'est pas valide (code d'état APNS : 8).|Aucune dimension|
|outgoing.apns.expiredchannel|Erreur de canal APNS arrivé à expiration|Nombre|Total|Nombre de jetons qui ont été invalidés par le canal de commentaires APNS.|Aucune dimension|
|outgoing.apns.invalidnotificationsize|Erreur de taille de notification APNS non valide|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car la charge utile était trop importante (code d'état APNS : 7).|Aucune dimension|
|outgoing.apns.pnserror|Erreurs APNS|Nombre|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec APNS.|Aucune dimension|
|outgoing.gcm.success|Notifications GCM réussies|Nombre|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.gcm.invalidcredentials|Erreurs d’autorisation GCM (informations d’identification non valides)|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|outgoing.gcm.badchannel|Erreur de canal GCM incorrect|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément registrationId contenu dans l'inscription n'a pas été reconnu (résultat GCM : inscription non valide).|Aucune dimension|
|outgoing.gcm.expiredchannel|Erreur de canal GCM arrivé à expiration|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément registrationId contenu dans l'inscription n'a pas été reconnu (résultat GCM : NotRegistered).|Aucune dimension|
|outgoing.gcm.throttled|Notifications GCM limitées|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car GCM a limité cette application (code d'état GCM : 501-599 ou résultat : non disponible).|Aucune dimension|
|outgoing.gcm.invalidnotificationformat|Format de notification GCM non valide|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car la charge utile n'était pas correctement mise en forme (résultat GCM : InvalidDataKey ou InvalidTtl).|Aucune dimension|
|outgoing.gcm.invalidnotificationsize|Erreur de taille de notification GCM non valide|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car la charge utile était trop importante (résultat GCM : MessageTooBig).|Aucune dimension|
|outgoing.gcm.wrongchannel|Erreur de canal GCM incorrect|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément registrationId contenu dans l'inscription n'est pas associé à l'application actuelle (résultat GCM : InvalidPackageName).|Aucune dimension|
|outgoing.gcm.pnserror|Erreurs GCM|Nombre|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec GCM.|Aucune dimension|
|outgoing.gcm.authenticationerror|Erreurs d’authentification GCM|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car le PNS n'a pas accepté les informations d'identification fournies, celles-ci sont bloquées ou l'élément SenderId n'est pas correctement configuré dans l'application (résultat GCM : MismatchedSenderId).|Aucune dimension|
|outgoing.mpns.success|Notifications MPNS réussies|Nombre|Total|Total des notifications réussies.|Aucune dimension|
|outgoing.mpns.invalidcredentials|Informations d’identification MPNS non valides|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|outgoing.mpns.badchannel|Erreur de canal incorrect MPNS|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément ChannelURI contenu dans l'inscription n'a pas été reconnu (état MPNS : 404 - Introuvable).|Aucune dimension|
|outgoing.mpns.throttled|Notifications MPNS limitées|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car MPNS limite cette application (WNS MPNS : 406 Non acceptable).|Aucune dimension|
|outgoing.mpns.invalidnotificationformat|Format de notification MPNS non valide|Nombre|Total|Nombre d’opérations Push en échec, car la charge utile de la notification était trop importante.|Aucune dimension|
|outgoing.mpns.channeldisconnected|Canal MPNS déconnecté|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément ChannelURI contenu dans l'inscription était déconnecté (état MPNS : 412 introuvable).|Aucune dimension|
|outgoing.mpns.dropped|Notifications MPNS supprimées|Nombre|Total|Nombre d'opérations d'envoi (push) qui ont été abandonnées par MPNS (en-tête de réponse MPNS : X-NotificationStatus: QueueFull ou Suppressed).|Aucune dimension|
|outgoing.mpns.pnserror|Erreurs MPNS|Nombre|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec MPNS.|Aucune dimension|
|outgoing.mpns.authenticationerror|Erreurs d’authentification MPNS|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|Aucune dimension|
|notificationhub.pushes|Toutes les notifications sortantes|Nombre|Total|Toutes les notifications sortantes du hub de notification|Aucune dimension|
|incoming.all.requests|Toutes les demandes entrantes|Nombre|Total|Nombre total des demandes entrantes pour un hub de notification|Aucune dimension|
|incoming.all.failedrequests|Toutes les requêtes entrantes ayant échoué|Nombre|Total|Nombre total des demandes entrantes ayant échoué pour un hub de notification|Aucune dimension|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Inodes libres Average_%|% Free Inodes|Nombre|Moyenne|Inodes libres Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Nombre|Moyenne|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Inodes utilisés Average_%|% Used Inodes|Nombre|Moyenne|Inodes utilisés Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Inodes utilisés Average_%|% Used Space|Nombre|Moyenne|Inodes utilisés Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Octets de lecture/s Average_Disk|Nb d’octets de lecture de disque/s |Nombre|Moyenne|Octets de lecture/s Average_Disk|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Nb d’opérations de lectures de disque/s |Nombre|Moyenne|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Nombre|Moyenne|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Octets d’écriture/s Average_Disk| Nb d’octets d’écriture de disque/s|Nombre|Moyenne|Octets d’écriture/s Average_Disk|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec| Nb d’opération d’écriture de disque/s|Nombre|Moyenne|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Mégaoctets Average_Free|Free Megabytes|Nombre|Moyenne|Mégaoctets Average_Free|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Nombre|Moyenne|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Nombre|Moyenne|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Nombre|Moyenne|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Nombre|Moyenne|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Nombre|Moyenne|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Nombre|Moyenne|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Nombre|Moyenne|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Nombre|Moyenne|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Nombre|Moyenne|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Nombre|Moyenne|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Nombre|Moyenne|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Nombre|Moyenne|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Nombre|Moyenne|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Nombre|Moyenne|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Total Bytes|Nombre|Moyenne|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Nombre|Moyenne|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Packets Received|Nombre|Moyenne|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Nombre|Moyenne|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Tx Errors|Nombre|Moyenne|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Nombre|Moyenne|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Nombre|Moyenne|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|Nombre|Moyenne|Average_Avg. Disk sec/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Nombre|Moyenne|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Nombre|Moyenne|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Nombre|Moyenne|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Nombre|Moyenne|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory kBytes|Nombre|Moyenne|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Nombre|Moyenne|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Nombre|Moyenne|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% Idle Time|Nombre|Moyenne|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Nombre|Moyenne|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Nombre|Moyenne|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Nombre|Moyenne|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Nombre|Moyenne|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Temps processeur Average_%|% temps processeur|Nombre|Moyenne|Temps processeur Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Nombre|Moyenne|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Nombre|Moyenne|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Nombre|Moyenne|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Nombre|Moyenne|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processus|Nombre|Moyenne|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Nombre|Moyenne|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Uptime|Nombre|Moyenne|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Utilisateurs|Nombre|Moyenne|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Nombre|Moyenne|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Nombre|Moyenne|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Longueur de file d’attente du disque Average_Current|Longueur de file d’attente actuelle du disque|Nombre|Moyenne|Longueur de file d’attente du disque Average_Current|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Nb d’opérations de lectures de disque/s |Nombre|Moyenne|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Nombre|Moyenne|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec| Nb d’opération d’écriture de disque/s|Nombre|Moyenne|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Mégaoctets Average_Free|Free Megabytes|Nombre|Moyenne|Mégaoctets Average_Free|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Nombre|Moyenne|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Nombre d’octets disponibles|Nombre|Moyenne|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Octets validés en cours d’utilisation Average_%|% d’octets validés utilisés|Nombre|Moyenne|Octets validés en cours d’utilisation Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Octets reçus/s|Nombre|Moyenne|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Octets envoyés/s|Nombre|Moyenne|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Nombre total d’octets/s|Nombre|Moyenne|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Temps processeur Average_%|% temps processeur|Nombre|Moyenne|Temps processeur Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Longueur de la file d’attente Average_Processor|Longueur de la file d’attente du processeur|Nombre|Moyenne|Longueur de la file d’attente Average_Processor|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Nombre|Total|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Mettre à jour|Mettre à jour|Nombre|Moyenne|Mettre à jour|Computer, Product, Classification, UpdateState, Optional, Approved|
|Événement|Événement|Nombre|Moyenne|Événement|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QueryDuration|Durée de la requête|Millisecondes|Moyenne|Durée de la requête DAX dans le dernier intervalle|Aucune dimension|
|QueryPoolJobQueueLength|Threads : longueur de la file d'attente des travaux du pool de requêtes|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de requêtes.|Aucune dimension|
|qpu_high_utilization_metric|Utilisation élevée du QPU|Nombre|Total|Utilisation élevée du QPU pendant la dernière minute, 1 pour une utilisation élevée du QPU, sinon 0|Aucune dimension|
|memory_metric|Mémoire|Octets|Moyenne|Mémoire. Plage de 0-3 Go pour A1, de 0-5 Go pour A2, de 0-10 Go pour A3, de 0-25 Go pour A4, de 0-50 Go pour A5 et de 0-100 Go pour A6|Aucune dimension|
|memory_thrashing_metric|Vidage de mémoire|Pourcentage|Moyenne|Vidage de mémoire moyenne.|Aucune dimension|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Nombre|Total|ListenerConnections ayant abouti pour Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Nombre|Total|ClientError sur ListenerConnections pour Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Nombre|Total|ServerError sur ListenerConnections pour Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Nombre|Total|SenderConnections ayant abouti pour Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Nombre|Total|ClientError sur SenderConnections pour Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Nombre|Total|ServerError sur SenderConnections pour Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Nombre|Total|Nombre total de ListenerConnections pour Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Nombre|Total|Nombre total de demandes SenderConnections pour Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Nombre|Total|Nombre total d’ActiveConnections pour Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Nombre|Total|Nombre total d’ActiveListeners pour Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Nombre|Total|Nombre total de BytesTransferred pour Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Nombre|Total|Nombre total de ListenerDisconnects pour Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Nombre|Total|Nombre total de SenderDisconnects pour Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SearchLatency|Latence de recherche|Secondes|Moyenne|Latence moyenne de recherche du service de recherche|Aucune dimension|
|SearchQueriesPerSecond|Requêtes de recherche par seconde|CountPerSecond|Moyenne|Requêtes de recherche par seconde pour le service de recherche|Aucune dimension|
|ThrottledSearchQueriesPercentage|Pourcentage de requêtes de recherche limitées|Pourcentage|Moyenne|Pourcentage de requêtes de recherche limitées par le service de recherche|Aucune dimension|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Requêtes ayant réussi (préversion)|Nombre|Total|Nombre total de demandes ayant réussi pour un espace de noms (préversion)|EntityName|
|ServerErrors|Erreurs de serveur. (Préversion)|Nombre|Total|Erreurs de serveur pour Microsoft.ServiceBus. (Préversion)|EntityName|
|UserErrors|Erreurs d’utilisateur. (Préversion)|Nombre|Total|Erreurs d’utilisateur pour Microsoft.ServiceBus. (Préversion)|EntityName|
|ThrottledRequests|Demandes limitées. (Préversion)|Nombre|Total|Demandes limitées pour Microsoft.ServiceBus. (Préversion)|EntityName|
|IncomingRequests|Demandes entrantes (préversion)|Nombre|Total|Demandes entrantes pour Microsoft.ServiceBus. (Préversion)|EntityName|
|IncomingMessages|Messages entrants (préversion)|Nombre|Total|Messages entrants pour Microsoft.ServiceBus. (Préversion)|EntityName|
|OutgoingMessages|Messages sortants (préversion)|Nombre|Total|Messages sortants pour Microsoft.ServiceBus. (Préversion)|EntityName|
|ActiveConnections|ActiveConnections (préversion)|Nombre|Total|Nombre total de connexions actives pour Microsoft.ServiceBus. (Préversion)|Aucune dimension|
|Taille|Taille (Préversion)|Octets|Moyenne|Taille d’une file d’attente/rubrique en octets. (Préversion)|EntityName|
|Messages|Nombre de messages dans une file d’attente/rubrique. (Préversion)|Nombre|Moyenne|Nombre de messages dans une file d’attente/rubrique. (Préversion)|EntityName|
|ActiveMessages|Nombre de messages actifs dans une file d’attente/rubrique. (Préversion)|Nombre|Moyenne|Nombre de messages actifs dans une file d’attente/rubrique. (Préversion)|EntityName|
|CPUXNS|Utilisation du processeur par espace de noms|Pourcentage|Maximale|Métrique d’utilisation du processeur de l’espace de noms Service Bus Premium|Aucune dimension|
|WSXNS|Utilisation de la taille mémoire par espace de noms|Pourcentage|Maximale|Métrique d’utilisation de la mémoire de l’espace de noms Service Bus Premium|Aucune dimension|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ConnectionCount|Nombre de connexions|Nombre|Maximale|Nombre de connexions utilisateur|Point de terminaison|
|MessageCount|Nombre de messages|Nombre|Total|Nombre total de messages.|Aucune dimension|
|InboundTraffic|Trafic entrant|Octets|Total|Trafic entrant de service|Aucune dimension|
|OutboundTraffic|Trafic sortant|Octets|Total|Trafic sortant de service|Aucune dimension|
|UserErrors|Erreurs d’utilisateur|Pourcentage|Maximale|Pourcentage d’erreurs d’utilisateur|Aucune dimension|
|SystemErrors|Erreurs système|Pourcentage|Maximale|Pourcentage d’erreurs système|Aucune dimension|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Aucune dimension|
|physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Moyenne|Pourcentage E/S des données|Aucune dimension|
|log_write_percent|Pourcentage E/S du journal|Pourcentage|Moyenne|Pourcentage E/S du journal|Aucune dimension|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|Aucune dimension|
|storage|Taille de base de données totale|Octets|Maximale|Taille de base de données totale|Aucune dimension|
|connection_successful|Connexions réussies|Nombre|Total|Connexions réussies|Aucune dimension|
|connection_failed|Connexions ayant échoué|Nombre|Total|Connexions ayant échoué|Aucune dimension|
|blocked_by_firewall|Bloqué par le pare-feu|Nombre|Total|Bloqué par le pare-feu|Aucune dimension|
|deadlock|Blocages|Nombre|Total|Blocages|Aucune dimension|
|storage_percent|Pourcentage de la taille de la base de données|Pourcentage|Maximale|Pourcentage de la taille de la base de données|Aucune dimension|
|xtp_storage_percent|Pourcentage de stockage OLTP en mémoire|Pourcentage|Moyenne|Pourcentage de stockage OLTP en mémoire|Aucune dimension|
|workers_percent|Pourcentage de travaux|Pourcentage|Moyenne|Pourcentage de travaux|Aucune dimension|
|sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|Aucune dimension|
|dtu_limit|Limite DTU|Nombre|Moyenne|Limite DTU|Aucune dimension|
|dtu_used|DTU utilisé|Nombre|Moyenne|DTU utilisé|Aucune dimension|
|dwu_limit|Limite DWU|Nombre|Maximale|Limite DWU|Aucune dimension|
|dwu_consumption_percent|Pourcentage DWU|Pourcentage|Maximale|Pourcentage DWU|Aucune dimension|
|dwu_used|DWU utilisé|Nombre|Maximale|DWU utilisé|Aucune dimension|
|dw_cpu_percent|Pourcentage d’utilisation de l’unité centrale au niveau du nœud DW|Pourcentage|Moyenne|Pourcentage d’utilisation de l’unité centrale au niveau du nœud DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Pourcentage E/S des données au niveau du nœud DW|Pourcentage|Moyenne|Pourcentage E/S des données au niveau du nœud DW|DwLogicalNodeId|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Aucune dimension|
|physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Moyenne|Pourcentage E/S des données|Aucune dimension|
|log_write_percent|Pourcentage E/S du journal|Pourcentage|Moyenne|Pourcentage E/S du journal|Aucune dimension|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|Aucune dimension|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|Aucune dimension|
|workers_percent|Pourcentage de travaux|Pourcentage|Moyenne|Pourcentage de travaux|Aucune dimension|
|sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|Aucune dimension|
|eDTU_limit|Limite eDTU|Nombre|Moyenne|Limite eDTU|Aucune dimension|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|Aucune dimension|
|eDTU_used|eDTU utilisé|Nombre|Moyenne|eDTU utilisé|Aucune dimension|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|Aucune dimension|
|xtp_storage_percent|Pourcentage de stockage OLTP en mémoire|Pourcentage|Moyenne|Pourcentage de stockage OLTP en mémoire|Aucune dimension|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|virtual_core_count|Nombre de cœurs virtuels|Nombre|Moyenne|Nombre de cœurs virtuels|Aucune dimension|
|avg_cpu_percent|Pourcentage d’UC moyenne|Pourcentage|Moyenne|Pourcentage d’UC moyenne|Aucune dimension|
|reserved_storage_mb|Espace de stockage réservé|Nombre|Moyenne|Espace de stockage réservé|Aucune dimension|
|storage_space_used_mb|Espace de stockage utilisé|Nombre|Moyenne|Espace de stockage utilisé|Aucune dimension|
|io_requests|Nombre de requêtes d’E/S|Nombre|Moyenne|Nombre de requêtes d’E/S|Aucune dimension|
|io_bytes_read|Octets d’E/S lus|Octets|Moyenne|Octets d’E/S lus|Aucune dimension|
|io_bytes_written|Octets d’E/S écrits|Octets|Moyenne|Octets d’E/S écrits|Aucune dimension|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Capacité utilisée|Octets|Total|Capacité de compte utilisée|Aucune dimension|
|Transactions|Transactions|Nombre|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName, Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName, Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName, Authentication|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Capacité d’objet blob|Octets|Total|Quantité de stockage utilisée par le service BLOB du compte de stockage, en octets.|/BlobType|
|BlobCount|Nombre d’objets blob|Nombre|Total|Nombre d’objets blob dans le service BLOB du compte de stockage.|/BlobType|
|ContainerCount|Nombre de conteneurs d’objets blob|Nombre|Moyenne|Nombre de conteneurs d’objets blob dans le service BLOB du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Nombre|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName, Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName, Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName, Authentication|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|Capacité de fichiers|Octets|Moyenne|Quantité de stockage utilisée par le service de Fichier du compte de stockage, en octets.|Aucune dimension|
|FileCount|Nombre de fichiers|Nombre|Moyenne|Nombre de fichiers dans le service de Fichier du compte de stockage.|Aucune dimension|
|FileShareCount|Nombre de partages de fichiers|Nombre|Moyenne|Nombre de partage de fichiers dans le service de Fichier du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Nombre|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName, Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName, Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName, Authentication|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Capacité de la file d’attente|Octets|Moyenne|Quantité de stockage utilisée par le service de File d’attente du compte de stockage, en octets.|Aucune dimension|
|QueueCount|Nombre de files d’attente|Nombre|Moyenne|Nombre de files d’attente dans le service de File d’attente du compte de stockage.|Aucune dimension|
|QueueMessageCount|Nombre de messages dans la file d’attente|Nombre|Moyenne|Nombre approximatif de messages en file d’attente dans le service de File d’attente du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Nombre|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName, Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName, Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName, Authentication|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Capacité de la table|Octets|Moyenne|Quantité de stockage utilisée par le service de Table du compte de stockage, en octets.|Aucune dimension|
|TableCount|Nombre de tables|Nombre|Moyenne|Nombre de tables dans le service de Table du compte de stockage.|Aucune dimension|
|TableEntityCount|Nombre d’entités de table|Nombre|Moyenne|Nombre d’entités de table dans le service de Table du compte de stockage.|Aucune dimension|
|Transactions|Transactions|Nombre|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType, GeoType, ApiName, Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType, ApiName, Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Moyenne|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Moyenne|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType, ApiName, Authentication|
|Disponibilité|Disponibilité|Pourcentage|Moyenne|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType, ApiName, Authentication|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ResourceUtilization|Utilisation de % d’unités de diffusion|Pourcentage|Maximale|Utilisation de % d’unités de diffusion|Aucune dimension|
|InputEvents|Événements d’entrée|Nombre|Total|Événements d’entrée|Aucune dimension|
|InputEventBytes|Octets des événements d’entrée|Octets|Total|Octets des événements d’entrée|Aucune dimension|
|LateInputEvents|Événements d’entrée tardifs|Nombre|Total|Événements d’entrée tardifs|Aucune dimension|
|OutputEvents|Événements de sortie|Nombre|Total|Événements de sortie|Aucune dimension|
|ConversionErrors|Erreurs de conversion de données|Nombre|Total|Erreurs de conversion de données|Aucune dimension|
|Errors|Erreurs d’exécution|Nombre|Total|Erreurs d’exécution|Aucune dimension|
|DroppedOrAdjustedEvents|Événements en désordre|Nombre|Total|Événements en désordre|Aucune dimension|
|AMLCalloutRequests|Requêtes de fonction|Nombre|Total|Requêtes de fonction|Aucune dimension|
|AMLCalloutFailedRequests|Requêtes de fonction ayant échoué|Nombre|Total|Requêtes de fonction ayant échoué|Aucune dimension|
|AMLCalloutInputEvents|Événements de fonction|Nombre|Total|Événements de fonction|Aucune dimension|
|DeserializationError|Erreurs de désérialisation d’entrée|Nombre|Total|Erreurs de désérialisation d’entrée|Aucune dimension|
|EarlyInputEvents|Événements d’entrée précoces|Nombre|Total|Événements d’entrée précoces|Aucune dimension|
|OutputWatermarkDelaySeconds|Délai en filigrane|Secondes|Maximale|Délai en filigrane|Aucune dimension|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Messages reçus en entrée|Nombre|Total|Nombre de messages lus à partir de la totalité des Event Hubs ou des sources d’événements IoT Hub|Aucune dimension|
|IngressReceivedInvalidMessages|Messages non valides reçus en entrée|Nombre|Total|Nombre de messages non valides lus à partir de la totalité des Event Hubs ou des sources d’événements IoT Hub|Aucune dimension|
|IngressReceivedBytes|Octets reçus en entrée|Octets|Total|Nombre d’octets lus à partir de toutes les sources d’événements|Aucune dimension|
|IngressStoredBytes|Octets stockés en entrée|Octets|Total|Taille totale des événements traités correctement et disponibles pour une requête|Aucune dimension|
|IngressStoredEvents|Événements stockés en entrée|Nombre|Total|Nombre d’événements aplatis traités correctement et disponibles pour une requête|Aucune dimension|
|IngressReceivedMessagesTimeLag|Retard des messages reçus en entrée|Secondes|Maximale|Différence entre l’heure à laquelle le message est placé en file d’attente dans la source d’événement et l’heure à laquelle il est traité en entrée|Aucune dimension|
|IngressReceivedMessagesCountLag|Décalage de nombre des messages reçus en entrée|Nombre|Moyenne|Différence entre le numéro de séquence du dernier message placé en file d’attente dans la partition source de l’événement et le numéro de séquence du message traité en entrée|Aucune dimension|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Messages reçus en entrée|Nombre|Total|Nombre de messages lus à partir de la source de l’événement|Aucune dimension|
|IngressReceivedInvalidMessages|Messages non valides reçus en entrée|Nombre|Total|Nombre de messages non valides lus à partir de la source de l’événement|Aucune dimension|
|IngressReceivedBytes|Octets reçus en entrée|Octets|Total|Nombre d’octets lus à partir de la source de l’événement|Aucune dimension|
|IngressStoredBytes|Octets stockés en entrée|Octets|Total|Taille totale des événements traités correctement et disponibles pour une requête|Aucune dimension|
|IngressStoredEvents|Événements stockés en entrée|Nombre|Total|Nombre d’événements aplatis traités correctement et disponibles pour une requête|Aucune dimension|
|IngressReceivedMessagesTimeLag|Retard des messages reçus en entrée|Secondes|Maximale|Différence entre l’heure à laquelle le message est placé en file d’attente dans la source d’événement et l’heure à laquelle il est traité en entrée|Aucune dimension|
|IngressReceivedMessagesCountLag|Décalage de nombre des messages reçus en entrée|Nombre|Moyenne|Différence entre le numéro de séquence du dernier message placé en file d’attente dans la partition source de l’événement et le numéro de séquence du message traité en entrée|Aucune dimension|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuPercentage|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Instance|
|DiskQueueLength|Longueur de file d'attente de disque|Nombre|Moyenne|Longueur de file d'attente de disque|Instance|
|HttpQueueLength|Longueur de la file d’attente HTTP|Nombre|Moyenne|Longueur de la file d’attente HTTP|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (à l’exclusion de Functions)

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|Instance|
|Demandes|Demandes|Nombre|Total|Requests|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Nombre|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Nombre|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Nombre|Total|Http 3xx|Instance|
|Http401|Http 401|Nombre|Total|Http 401|Instance|
|Http403|Http 403|Nombre|Total|Http 403|Instance|
|Http404|Http 404|Nombre|Total|Http 404|Instance|
|Http406|Http 406|Nombre|Total|Http 406|Instance|
|Http4xx|Http 4xx|Nombre|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|Instance|
|AppConnections|connexions|Nombre|Moyenne|connexions|Instance|
|Handles|Nombre de descripteurs|Nombre|Moyenne|Nombre de descripteurs|Instance|
|Threads|Nombre de threads|Nombre|Moyenne|Nombre de threads|Instance|
|PrivateBytes|Octets privés|Octets|Moyenne|Octets privés|Instance|
|IoReadBytesPerSecond|Octets lus par seconde (E/S)|BytesPerSecond|Total|Octets lus par seconde (E/S)|Instance|
|IoWriteBytesPerSecond|Octets écrits par seconde (E/S)|BytesPerSecond|Total|Octets écrits par seconde (E/S)|Instance|
|IoOtherBytesPerSecond|Autres octets par seconde (E/S)|BytesPerSecond|Total|Autres octets par seconde (E/S)|Instance|
|IoReadOperationsPerSecond|Opérations de lecture par seconde (E/S)|BytesPerSecond|Total|Opérations de lecture par seconde (E/S)|Instance|
|IoWriteOperationsPerSecond|Opérations d’écriture par seconde (E/S)|BytesPerSecond|Total|Opérations d’écriture par seconde (E/S)|Instance|
|IoOtherOperationsPerSecond|Autres opérations par seconde (E/S)|BytesPerSecond|Total|Autres opérations par seconde (E/S)|Instance|
|RequestsInApplicationQueue|Demandes dans la file d’attente d’application|Nombre|Moyenne|Demandes dans la file d’attente d’application|Instance|
|CurrentAssemblies|Assemblys actuels|Nombre|Moyenne|Assemblys actuels|Instance|
|TotalAppDomains|Total des domaines d’application|Nombre|Moyenne|Total des domaines d’application|Instance|
|TotalAppDomainsUnloaded|Total des domaines d’application déchargés|Nombre|Moyenne|Total des domaines d’application déchargés|Instance|
|Gen0Collections|Garbage collections de génération 0|Nombre|Total|Garbage collections de génération 0|Instance|
|Gen1Collections|Garbage collections de génération 1|Nombre|Total|Garbage collections de génération 1|Instance|
|Gen2Collections|Garbage collections de génération 2|Nombre|Total|Garbage collections de génération 2|Instance|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Functions)

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|Instance|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Nombre|Total|Unités d’exécution de fonctions|Instance|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Nombre|Total|Nombre d’exécutions de fonctions|Instance|
|PrivateBytes|Octets privés|Octets|Moyenne|Octets privés|Instance|
|IoReadBytesPerSecond|Octets lus par seconde (E/S)|BytesPerSecond|Total|Octets lus par seconde (E/S)|Instance|
|IoWriteBytesPerSecond|Octets écrits par seconde (E/S)|BytesPerSecond|Total|Octets écrits par seconde (E/S)|Instance|
|IoOtherBytesPerSecond|Autres octets par seconde (E/S)|BytesPerSecond|Total|Autres octets par seconde (E/S)|Instance|
|IoReadOperationsPerSecond|Opérations de lecture par seconde (E/S)|BytesPerSecond|Total|Opérations de lecture par seconde (E/S)|Instance|
|IoWriteOperationsPerSecond|Opérations d’écriture par seconde (E/S)|BytesPerSecond|Total|Opérations d’écriture par seconde (E/S)|Instance|
|IoOtherOperationsPerSecond|Autres opérations par seconde (E/S)|BytesPerSecond|Total|Autres opérations par seconde (E/S)|Instance|
|RequestsInApplicationQueue|Demandes dans la file d’attente d’application|Nombre|Moyenne|Demandes dans la file d’attente d’application|Instance|
|CurrentAssemblies|Assemblys actuels|Nombre|Moyenne|Assemblys actuels|Instance|
|TotalAppDomains|Total des domaines d’application|Nombre|Moyenne|Total des domaines d’application|Instance|
|TotalAppDomainsUnloaded|Total des domaines d’application déchargés|Nombre|Moyenne|Total des domaines d’application déchargés|Instance|
|Gen0Collections|Garbage collections de génération 0|Nombre|Total|Garbage collections de génération 0|Instance|
|Gen1Collections|Garbage collections de génération 1|Nombre|Total|Garbage collections de génération 1|Instance|
|Gen2Collections|Garbage collections de génération 2|Nombre|Total|Garbage collections de génération 2|Instance|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|Instance|
|Demandes|Demandes|Nombre|Total|Requests|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Nombre|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Nombre|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Nombre|Total|Http 3xx|Instance|
|Http401|Http 401|Nombre|Total|Http 401|Instance|
|Http403|Http 403|Nombre|Total|Http 403|Instance|
|Http404|Http 404|Nombre|Total|Http 404|Instance|
|Http406|Http 406|Nombre|Total|Http 406|Instance|
|Http4xx|Http 4xx|Nombre|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|Instance|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Nombre|Total|Unités d’exécution de fonctions|Instance|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Nombre|Total|Nombre d’exécutions de fonctions|Instance|
|AppConnections|connexions|Nombre|Moyenne|connexions|Instance|
|Handles|Nombre de descripteurs|Nombre|Moyenne|Nombre de descripteurs|Instance|
|Threads|Nombre de threads|Nombre|Moyenne|Nombre de threads|Instance|
|PrivateBytes|Octets privés|Octets|Moyenne|Octets privés|Instance|
|IoReadBytesPerSecond|Octets lus par seconde (E/S)|BytesPerSecond|Total|Octets lus par seconde (E/S)|Instance|
|IoWriteBytesPerSecond|Octets écrits par seconde (E/S)|BytesPerSecond|Total|Octets écrits par seconde (E/S)|Instance|
|IoOtherBytesPerSecond|Autres octets par seconde (E/S)|BytesPerSecond|Total|Autres octets par seconde (E/S)|Instance|
|IoReadOperationsPerSecond|Opérations de lecture par seconde (E/S)|BytesPerSecond|Total|Opérations de lecture par seconde (E/S)|Instance|
|IoWriteOperationsPerSecond|Opérations d’écriture par seconde (E/S)|BytesPerSecond|Total|Opérations d’écriture par seconde (E/S)|Instance|
|IoOtherOperationsPerSecond|Autres opérations par seconde (E/S)|BytesPerSecond|Total|Autres opérations par seconde (E/S)|Instance|
|RequestsInApplicationQueue|Demandes dans la file d’attente d’application|Nombre|Moyenne|Demandes dans la file d’attente d’application|Instance|
|CurrentAssemblies|Assemblys actuels|Nombre|Moyenne|Assemblys actuels|Instance|
|TotalAppDomains|Total des domaines d’application|Nombre|Moyenne|Total des domaines d’application|Instance|
|TotalAppDomainsUnloaded|Total des domaines d’application déchargés|Nombre|Moyenne|Total des domaines d’application déchargés|Instance|
|Gen0Collections|Garbage collections de génération 0|Nombre|Total|Garbage collections de génération 0|Instance|
|Gen1Collections|Garbage collections de génération 1|Nombre|Total|Garbage collections de génération 1|Instance|
|Gen2Collections|Garbage collections de génération 2|Nombre|Total|Garbage collections de génération 2|Instance|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Demandes|Demandes|Nombre|Total|Requests|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Nombre|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Nombre|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Nombre|Total|Http 3xx|Instance|
|Http401|Http 401|Nombre|Total|Http 401|Instance|
|Http403|Http 403|Nombre|Total|Http 403|Instance|
|Http404|Http 404|Nombre|Total|Http 404|Instance|
|Http406|Http 406|Nombre|Total|Http 406|Instance|
|Http4xx|Http 4xx|Nombre|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|Instance|
|CpuPercentage|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Instance|
|DiskQueueLength|Longueur de file d'attente de disque|Nombre|Moyenne|Longueur de file d'attente de disque|Instance|
|HttpQueueLength|Longueur de la file d’attente HTTP|Nombre|Moyenne|Longueur de la file d’attente HTTP|Instance|
|ActiveRequests|Requêtes actives|Nombre|Total|Requêtes actives|Instance|
|TotalFrontEnds|Nombre total de serveurs frontaux|Nombre|Moyenne|Nombre total de serveurs frontaux|Aucune dimension|
|SmallAppServicePlanInstances|Workers de plan App Service de petite taille|Nombre|Moyenne|Workers de plan App Service de petite taille|Aucune dimension|
|MediumAppServicePlanInstances|Workers de plan App Service de taille moyenne|Nombre|Moyenne|Workers de plan App Service de taille moyenne|Aucune dimension|
|LargeAppServicePlanInstances|Workers de plan App Service de grande taille|Nombre|Moyenne|Workers de plan App Service de grande taille|Aucune dimension|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|WorkersTotal|Nombre total de workers|Nombre|Moyenne|Nombre total de workers|Aucune dimension|
|WorkersAvailable|Workers disponibles|Nombre|Moyenne|Workers disponibles|Aucune dimension|
|WorkersUsed|Workers utilisés|Nombre|Moyenne|Workers utilisés|Aucune dimension|
|CpuPercentage|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|Instance|

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les mesures dans Azure Monitor](../../azure-monitor/platform/data-collection.md)
* [Créer des alertes sur les mesures](../../azure-monitor/platform/alerts-overview.md)
* [Exporter des mesures vers le stockage, un hub d’événements ou Log Analytics](../../azure-monitor/platform/diagnostic-logs-overview.md)
