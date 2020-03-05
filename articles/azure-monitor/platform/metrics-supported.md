---
title: Métriques prises en charge par Azure Monitor par type de ressource
description: Liste des métriques disponibles pour chaque type de ressource avec Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3e43d2baf4337e7a986d59c47f805183a920c7a1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659677"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métriques prises en charge avec Azure Monitor

Azure Monitor offre plusieurs moyens d’interagir avec les métriques, y compris en créant des graphiques dans le portail, en y accédant via l’API REST ou en envoyant des requêtes avec PowerShell ou l’interface CLI. Voici une liste complète de toutes les métriques actuellement offertes par le pipeline de métrique d’Azure Monitor. D’autres métriques peuvent être disponibles dans le portail ou via les API héritées. La liste ci-dessous englobe uniquement les métriques disponibles en utilisant le pipeline de métriques Azure Monitor consolidé. Les métriques sont organisées par espace de noms. Pour obtenir la liste des services et des espaces de noms qui leur appartiennent, consultez [Fournisseurs de ressources pour les services Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). Pour interroger ces métriques et y accéder par programmation, veuillez utiliser [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions).

> [!NOTE]
> L’envoi de métriques multidimensionnelles via les paramètres de diagnostic n’est pas pris en charge actuellement. Les métriques à plusieurs dimensions sont exportées en tant que métriques dimensionnelles uniques aplaties, puis agrégées dans les valeurs de la dimension.
>
> *Par exemple* : La métrique « Messages entrants » sur un Event Hub peut être examinée et représentée sur un niveau par file d’attente. Toutefois, lors de l’exportation via les paramètres de diagnostic, la métrique est représentée sous la forme de tous les messages entrants, dans toutes les files d’attente de l’Event Hub.
>
> Pour obtenir la liste des métriques de plateforme exportables par le biais des paramètres de diagnostic, consultez [cet article](metrics-supported-export-diagnostic-settings.md).



## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Plage de 0 à 100 pour S1, de 0 à 200 pour S2 et de 0 à 400 pour S4|ServerResourceType|
|memory_metric|Mémoire|Octets|Average|Mémoire. Plage de 0 à 25 Go pour S1, de 0 à 50 Go pour S2 et de 0 à 100 Go pour S4|ServerResourceType|
|private_bytes_metric|Octets privés|Octets|Average|Octets privés.|ServerResourceType|
|virtual_bytes_metric|Octets virtuels|Octets|Average|Octets virtuels.|ServerResourceType|
|TotalConnectionRequests|Nombre total de demandes de connexion|Count|Average|Nombre total de demandes de connexion. Il s’agit des arrivées.|ServerResourceType|
|SuccessfullConnectionsPerSec|Connexions réussies par seconde|CountPerSecond|Average|Taux de connexions terminées réussies.|ServerResourceType|
|TotalConnectionFailures|Nombre total d’échecs de connexion|Count|Average|Total des échecs de tentatives de connexion.|ServerResourceType|
|CurrentUserSessions|Sessions utilisateur actuelles|Count|Average|Nombre actuel de sessions utilisateur établies.|ServerResourceType|
|QueryPoolBusyThreads|Threads occupés du pool de threads de requêtes|Count|Average|Nombre de threads occupés dans le pool de threads de requêtes.|ServerResourceType|
|CommandPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de commandes|Count|Average|Nombre de travaux contenus dans la file d’attente du pool de threads de commandes.|ServerResourceType|
|ProcessingPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de traitement|Count|Average|Nombre de travaux autres que d’E/S contenus dans la file d’attente du pool de threads de traitement.|ServerResourceType|
|CurrentConnections|Connexion : Connexions en cours|Count|Average|Nombre actuel de connexions client établies.|ServerResourceType|
|CleanerCurrentPrice|Mémoire : prix actuel du nettoyage|Count|Average|Prix actuel de la mémoire, $/octet/temps, normalisé à 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Mémoire : mémoire de nettoyage réductible|Octets|Average|Quantité de mémoire, en octets, qui doit être vidée par le nettoyage en arrière-plan.|ServerResourceType|
|CleanerMemoryNonshrinkable|Mémoire : mémoire de nettoyage non réductible|Octets|Average|Quantité de mémoire, en octets, qui ne doit pas être vidée par le nettoyage en arrière-plan.|ServerResourceType|
|MemoryUsage|Mémoire : Utilisation de la mémoire|Octets|Average|Utilisation de la mémoire du processus serveur telle qu’utilisée dans le calcul du coût de la mémoire de nettoyage. Équivaut au compteur Process\PrivateBytes, plus la taille des données mappées en mémoire, en ignorant la mémoire mappée ou allouée par le moteur d’analyse de mémoire xVelocity (VertiPaq) dépassant la limite de mémoire du moteur xVelocity.|ServerResourceType|
|MemoryLimitHard|Mémoire : limite inconditionnelle de la mémoire|Octets|Average|Limite de mémoire physique, du fichier de configuration.|ServerResourceType|
|MemoryLimitHigh|Mémoire : limite haute de la mémoire|Octets|Average|Limite de mémoire élevée, du fichier de configuration.|ServerResourceType|
|MemoryLimitLow|Mémoire : limite basse de la mémoire|Octets|Average|Limite de mémoire basse, du fichier de configuration.|ServerResourceType|
|MemoryLimitVertiPaq|Mémoire : limite de la mémoire VertiPaq|Octets|Average|Limite en mémoire, du fichier de configuration.|ServerResourceType|
|Quota|Mémoire : Quota|Octets|Average|Quota de mémoire actuel, en octets. Le quota de mémoire est également appelé réserve de mémoire ou d’allocation.|ServerResourceType|
|QuotaBlocked|Mémoire : quota bloqué|Count|Average|Nombre actuel de requêtes de quota qui sont bloquées en attendant la libération d’autres quotas de mémoire.|ServerResourceType|
|VertiPaqNonpaged|Mémoire : mémoire non paginée VertiPaq|Octets|Average|Octets de mémoire verrouillée dans la plage de travail pour utilisation par le moteur en mémoire.|ServerResourceType|
|VertiPaqPaged|Mémoire : mémoire paginée VertiPaq|Octets|Average|Octets de mémoire paginée utilisée pour les données en mémoire.|ServerResourceType|
|RowsReadPerSec|Traitement : lignes lues par seconde|CountPerSecond|Average|Taux de lignes lues à partir de toutes les bases de données relationnelles.|ServerResourceType|
|RowsConvertedPerSec|Traitement : lignes converties par seconde|CountPerSecond|Average|Taux de lignes converties lors du traitement.|ServerResourceType|
|RowsWrittenPerSec|Traitement : lignes écrites par seconde|CountPerSecond|Average|Taux de lignes écrites lors du traitement.|ServerResourceType|
|CommandPoolBusyThreads|Threads : threads occupés du pool de commandes|Count|Average|Nombre de threads occupés dans le pool de threads de commandes.|ServerResourceType|
|CommandPoolIdleThreads|Threads : threads inactifs du pool de commandes|Count|Average|Nombre de threads inactifs dans le pool de threads de commandes.|ServerResourceType|
|LongParsingBusyThreads|Threads : threads occupés d'analyse longue|Count|Average|Nombre de threads occupés dans le pool de threads d’analyse longue.|ServerResourceType|
|LongParsingIdleThreads|Threads : threads inactifs d'analyse longue|Count|Average|Nombre de threads inactifs dans le pool de threads d’analyse longue.|ServerResourceType|
|LongParsingJobQueueLength|Threads : longueur de la file d'attente des travaux d'analyse longue|Count|Average|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse longue.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads : threads des travaux d'E/S occupés du pool de traitement|Count|Average|Nombre de threads pour les travaux d’E/S en cours d’exécution dans le pool de threads de traitement.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads : threads autres que les threads d'E/S occupés du pool de traitement|Count|Average|Nombre de threads pour les travaux autres que d’E/S en cours d’exécution dans le pool de threads de traitement.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads : longueur de la file des travaux d'E/S du pool de traitement|Count|Average|Nombre de travaux d’E/S contenus dans la file d’attente du pool de threads de traitement.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads : threads des travaux d'E/S inactifs du pool de traitement|Count|Average|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads : threads autres que les threads d'E/S inactifs du pool de traitement|Count|Average|Nombre de threads inactifs le pool de threads de traitement dédiés aux travaux autres qu’E/S.|ServerResourceType|
|QueryPoolIdleThreads|Threads : threads inactifs du pool de requêtes|Count|Average|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|ServerResourceType|
|QueryPoolJobQueueLength|Threads : longueur de la file d'attente des travaux du pool de requêtes|Count|Average|Nombre de travaux contenus dans la file d’attente du pool de threads de requêtes.|ServerResourceType|
|ShortParsingBusyThreads|Threads : threads occupés d'analyse courte|Count|Average|Nombre de threads occupés dans le pool de threads d’analyse courte.|ServerResourceType|
|ShortParsingIdleThreads|Threads : threads inactifs d'analyse courte|Count|Average|Nombre de threads inactifs dans le pool de threads d’analyse courte.|ServerResourceType|
|ShortParsingJobQueueLength|Threads : longueur de la file d'attente des travaux d'analyse courte|Count|Average|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse courte.|ServerResourceType|
|memory_thrashing_metric|Vidage de mémoire|Pourcentage|Average|Vidage de mémoire moyenne.|ServerResourceType|
|mashup_engine_qpu_metric|QPU du moteur M|Count|Average|Utilisation des QPU par les processus de moteur mashup|ServerResourceType|
|mashup_engine_memory_metric|Mémoire du moteur M|Octets|Average|Utilisation de la mémoire par les processus de moteur mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Octets privés du moteur M|Octets|Average|Utilisation des octets privés par les processus de moteur mashup.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Octets virtuels du moteur M|Octets|Average|Utilisation des octets virtuels par les processus de moteur mashup.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalRequests|Nombre total de demandes de la passerelle (déprécié)|Count|Total|Nombre de demandes de la passerelle : utilisez une métrique de demande multi-dimension avec la dimension GatewayResponseCodeCategory à la place|Location,Hostname|
|SuccessfulRequests|Demandes de la passerelle ayant abouti (déprécié)|Count|Total|Nombre de demandes de la passerelle ayant abouti : utilisez une métrique de demande multi-dimension avec la dimension GatewayResponseCodeCategory à la place|Location,Hostname|
|UnauthorizedRequests|Demandes de la passerelle non autorisées (déprécié)|Count|Total|Nombre de demandes de la passerelle non autorisées : utilisez une métrique de demande multi-dimension avec la dimension GatewayResponseCodeCategory à la place|Location,Hostname|
|FailedRequests|Demandes de la passerelle ayant échoué (déprécié)|Count|Total|Nombre de défaillances des demandes de la passerelle : utilisez une métrique de demande multi-dimension avec la dimension GatewayResponseCodeCategory à la place|Location,Hostname|
|OtherRequests|Autres demandes de la passerelle (déprécié)|Count|Total|Nombre d’autres demandes de la passerelle : utilisez une métrique de demande multi-dimension avec la dimension GatewayResponseCodeCategory à la place|Location,Hostname|
|Duration|Durée globale des demandes de passerelle|Millisecondes|Average|Durée globale des demandes de passerelle en millisecondes|Location,Hostname|
|BackendDuration|Durées des demandes back-end|Millisecondes|Average|Durée des demandes back-end, en millisecondes|Location,Hostname|
|Capacité|Capacité|Pourcentage|Average|Métrique d’utilisation pour le service ApiManagement|Emplacement|
|EventHubTotalEvents|Nombre total d'événements EventHub|Count|Total|Nombre d’événements envoyés à EventHub|Emplacement|
|EventHubSuccessfulEvents|Événements EventHub réussis|Count|Total|Nombre d’événements EventHub réussis|Emplacement|
|EventHubTotalFailedEvents|Événements EventHub non réussis|Count|Total|Nombre d’événements EventHub non réussis|Emplacement|
|EventHubRejectedEvents|Événements EventHub rejetés|Count|Total|Nombre d’événements EventHub rejetés (configuration incorrecte ou non autorisée)|Emplacement|
|EventHubThrottledEvents|Événements EventHub limités|Count|Total|Nombre d’événements EventHub limités|Emplacement|
|EventHubTimedoutEvents|Événements EventHub expirés|Count|Total|Nombre d'événements EventHub expirés|Emplacement|
|EventHubDroppedEvents|Événements EventHub supprimés|Count|Total|Nombre d’événements ignorés car la limite de taille de la file d’attente a été atteinte|Emplacement|
|EventHubTotalBytesSent|Taille des événements EventHub|Octets|Total|Taille totale des événements EventHub en octets|Emplacement|
|Demandes|Demandes|Count|Total|Métriques de demande de la passerelle avec plusieurs dimensions|Location,Hostname,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Count|Count|Nombre total de requêtes HTTP entrantes.|None|
|FailedHttpRequestCount|FailedHttpRequestCount|Count|Count|Requêtes HTTP ayant échoué.|None|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Count|Average|Latence sur une requête HTTP.|None|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Pourcentage d’utilisation du processeur système|Pourcentage|Average|Utilisation récente du processeur pour l’ensemble du système|AppName,Pod|
|AppCpuUsagePercentage|Pourcentage d’utilisation du processeur d’application|Pourcentage|Average|Pourcentage d’utilisation du processeur JVM d’application|AppName,Pod|
|AppMemoryCommitted|Mémoire d’application affectée|Octets|Average|Mémoire affectée à JVM en octets|AppName,Pod|
|AppMemoryUsed|Mémoire d’application utilisée|Octets|Average|Mémoire d’application utilisée en octets|AppName,Pod|
|AppMemoryMax|Mémoire d’application maximale|Octets|Maximale|Quantité maximale de mémoire en octets utilisable pour la gestion de la mémoire|AppName,Pod|
|MaxOldGenMemoryPoolBytes|Taille maximale des données d’ancienne génération disponibles|Octets|Average|Taille maximale du pool de mémoire d’ancienne génération|AppName,Pod|
|OldGenMemoryPoolBytes|Taille des données d’ancienne génération|Octets|Average|Taille du pool de mémoire d’ancienne génération après un GC complet|AppName,Pod|
|OldGenPromotedBytes|Promouvoir à la taille des données d’ancienne génération|Octets|Maximale|Nombre d’augmentations positives de la taille du pool de mémoire d’ancienne génération avant l’application du GC jusqu’au terme de cette application|AppName,Pod|
|YoungGenPromotedBytes|Promouvoir à la taille des données de nouvelle génération|Octets|Maximale|Incrémenté pour une augmentation de la taille du pool de mémoire de nouvelle génération après un GC avant le suivant|AppName,Pod|
|GCPauseTotalCount|Nombre d’interruptions du GC|Count|Total|Nombre d’interruptions du GC|AppName,Pod|
|GCPauseTotalTime|Durée totale de pause du GC|Millisecondes|Total|Durée totale de pause du GC|AppName,Pod|
|TomcatSentBytes|Nombre total d’octets envoyés par Tomcat|Octets|Total|Nombre total d’octets envoyés par Tomcat|AppName,Pod|
|TomcatReceivedBytes|Nombre total d’octets reçus par Tomcat|Octets|Total|Nombre total d’octets reçus par Tomcat|AppName,Pod|
|TomcatRequestTotalTime|Durée totale des demandes Tomcat|Millisecondes|Total|Durée totale des demandes Tomcat|AppName,Pod|
|TomcatRequestTotalCount|Nombre total de demandes Tomcat|Count|Total|Nombre total de demandes Tomcat|AppName,Pod|
|TomcatResponseAvgTime|Durée moyenne des demandes Tomcat|Millisecondes|Average|Durée moyenne des demandes Tomcat|AppName,Pod|
|TomcatRequestMaxTime|Durée maximale des demandes Tomcat|Millisecondes|Maximale|Durée maximale des demandes Tomcat|AppName,Pod|
|TomcatErrorCount|Erreur globale Tomcat|Count|Total|Erreur globale Tomcat|AppName,Pod|
|TomcatSessionActiveMaxCount|Nombre maximal de sessions actives Tomcat|Count|Total|Nombre maximal de sessions actives Tomcat|AppName,Pod|
|TomcatSessionAliveMaxTime|Durée maximale des sessions actives Tomcat|Millisecondes|Maximale|Durée maximale des sessions actives Tomcat|AppName,Pod|
|TomcatSessionActiveCurrentCount|Nombre de sessions actives Tomcat|Count|Total|Nombre de sessions actives Tomcat|AppName,Pod|
|TomcatSessionCreatedCount|Nombre de sessions Tomcat créées|Count|Total|Nombre de sessions Tomcat créées|AppName,Pod|
|TomcatSessionExpiredCount|Nombre de sessions Tomcat ayant expiré|Count|Total|Nombre de sessions Tomcat ayant expiré|AppName,Pod|
|TomcatSessionRejectedCount|Nombre de sessions Tomcat rejetées|Count|Total|Nombre de sessions Tomcat rejetées|AppName,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalJob|Nombre total de travaux|Count|Total|Nombre total de travaux|Runbook,Status|
|TotalUpdateDeploymentRuns|Nombre total d’exécutions de déploiement de mises à jour|Count|Total|Nombre total d’exécutions de déploiement de mises à jour logicielles|SoftwareUpdateConfigurationName,Status|
|TotalUpdateDeploymentMachineRuns|Nombre total d’exécutions de déploiement de mises à jour de machines|Count|Total|Nombre total d'exécutions de déploiement de mises à jour logicielles de machines lors d'une exécution de déploiement de mises à jour logicielles|SoftwareUpdateConfigurationName,Status,TargetComputer,SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CoreCount|Nombre de cœurs dédiés|Count|Total|Nombre total de cœurs dédiés dans le compte Batch|None|
|TotalNodeCount|Nombre de nœuds dédiés|Count|Total|Nombre total de nœuds dédiés dans le compte Batch|None|
|LowPriorityCoreCount|Nombre de cœurs à priorité basse|Count|Total|Nombre total de cœurs à priorité basse dans le compte Batch|None|
|TotalLowPriorityNodeCount|Nombre de nœuds à priorité basse|Count|Total|Nombre total de nœuds à priorité basse dans le compte Batch|None|
|CreatingNodeCount|Nombre de nœuds créés|Count|Total|Nombre de nœuds en cours de création|None|
|StartingNodeCount|Nombre de nœuds de départ|Count|Total|Nœuds de nœuds de départ|None|
|WaitingForStartTaskNodeCount|Nombre de nœuds en attente de démarrage de tâche|Count|Total|Nombre de nœuds en attente de la fin d’une tâche de démarrage|None|
|StartTaskFailedNodeCount|Nombre de nœuds pour lesquels le démarrage d’une tâche a échoué|Count|Total|Le nombre de nœuds pour lesquels le démarrage d’une tâche a échoué|None|
|IdleNodeCount|Nombre de nœuds inactifs|Count|Total|Le nombre de nœuds inactifs|None|
|OfflineNodeCount|Nombre de nœuds hors ligne|Count|Total|Le nombre de nœuds hors ligne|None|
|RebootingNodeCount|Nombre de nœuds en cours de redémarrage|Count|Total|Le nombre de nœuds en cours de redémarrage|None|
|ReimagingNodeCount|Nombre de nœuds en cours de réimageage|Count|Total|Le nombre de nœuds en cours de réimageage|None|
|RunningNodeCount|Nombre de nœuds en cours d’exécution|Count|Total|Le nombre de nœuds en cours d’exécution|None|
|LeavingPoolNodeCount|Nombre de nœuds quittant le pool|Count|Total|Le nombre de nœuds quittant le pool|None|
|UnusableNodeCount|Nombre de nœuds inutilisables|Count|Total|Le nombre de nœuds inutilisables|None|
|PreemptedNodeCount|Nombre de nœuds reportés|Count|Total|Nombre de nœuds reportés|None|
|TaskStartEvent|Événements de lancement de tâche|Count|Total|Nombre total de tâches ayant démarré|None|
|TaskCompleteEvent|Événements de fin de tâche|Count|Total|Le nombre total de tâches terminées|None|
|TaskFailEvent|Événements d’échec de tâches|Count|Total|Le nombre total de tâches terminées dans un état d’échec|None|
|PoolCreateEvent|Événements de création de pool|Count|Total|Nombre total de pools créés|None|
|PoolResizeStartEvent|Événements de démarrage de redimensionnement de pool|Count|Total|Nombre total de redimensionnements de pool ayant démarré|None|
|PoolResizeCompleteEvent|Événements de fin de redimensionnement de pool|Count|Total|Nombre total de redimensionnements de pool terminés|None|
|PoolDeleteStartEvent|Événements de démarrage de suppression de pool|Count|Total|Nombre total de suppressions de pool ayant démarré|None|
|PoolDeleteCompleteEvent|Événements de suppression de pool terminés|Count|Total|Nombre total de suppressions de pool terminées|None|
|JobDeleteCompleteEvent|Événements de fin de suppression de travail|Count|Total|Nombre total de travaux correctement supprimés.|None|
|JobDeleteStartEvent|Événements de démarrage de suppression de travail|Count|Total|Nombre total de travaux demandés pour être supprimés.|None|
|JobDisableCompleteEvent|Événements de fin de désactivation de travail|Count|Total|Nombre total de travaux correctement supprimés.|None|
|JobDisableStartEvent|Événements de démarrage de désactivation de travail|Count|Total|Nombre total de travaux demandés pour être désactivés.|None|
|JobStartEvent|Événements de démarrage de travail|Count|Total|Nombre total de travaux correctement démarrés.|None|
|JobTerminateCompleteEvent|Événements de fin de travail terminé|Count|Total|Nombre total de travaux correctement terminés.|None|
|JobTerminateCompleteEvent|Événements de démarrage de fin de travail|Count|Total|Nombre total de travaux demandés pour être terminés.|None|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Travail envoyé|Travail envoyé|Count|Total|Nombre de travaux envoyés|Scenario,ClusterName|
|Travail effectué|Travail effectué|Count|Total|Nombre de travaux effectués|Scenario,ClusterName,ResultType|
|Nombre total de nœuds|Nombre total de nœuds|Count|Average|Nombre total de nœuds|Scenario,ClusterName|
|Nœuds actifs|Nœuds actifs|Count|Average|Le nombre de nœuds en cours d’exécution|Scenario,ClusterName|
|Nœuds inactifs|Nœuds inactifs|Count|Average|Le nombre de nœuds inactifs|Scenario,ClusterName|
|Nœuds inutilisables|Nœuds inutilisables|Count|Average|Le nombre de nœuds inutilisables|Scenario,ClusterName|
|Nœuds reportés|Nœuds reportés|Count|Average|Nombre de nœuds reportés|Scenario,ClusterName|
|Nœuds sortants|Nœuds sortants|Count|Average|Nombre de nœuds sortants|Scenario,ClusterName|
|Nombre total de cœurs|Nombre total de cœurs|Count|Average|Nombre total de cœurs|Scenario,ClusterName|
|Cœurs actifs|Cœurs actifs|Count|Average|Nombre de cœurs actifs|Scenario,ClusterName|
|Cœurs inactifs|Cœurs inactifs|Count|Average|Nombre de cœurs inactifs|Scenario,ClusterName|
|Cœurs inutilisables|Cœurs inutilisables|Count|Average|Nombre de cœurs inutilisables|Scenario,ClusterName|
|Cœurs réquisitionnés|Cœurs réquisitionnés|Count|Average|Nombre de cœurs réquisitionnés|Scenario,ClusterName|
|Cœurs sortants|Cœurs sortants|Count|Average|Nombre de cœurs sortants|Scenario,ClusterName|
|Pourcentage d’utilisation du quota|Pourcentage d’utilisation du quota|Count|Average|Pourcentage de quota utilisé|Scenario,ClusterName,VmFamilyName,VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Pourcentage d’utilisation du processeur|Pourcentage|Maximale|Pourcentage d’utilisation du processeur|Nœud|
|MemoryUsage|Utilisation de la mémoire|Octets|Average|Utilisation de la mémoire|Nœud|
|MemoryLimit|Limite de mémoire|Octets|Average|Limite de mémoire|Nœud|
|MemoryUsagePercentageInDouble|Pourcentage d’utilisation de la mémoire|Pourcentage|Average|Pourcentage d’utilisation de la mémoire|Nœud|
|StorageUsage|Utilisation du stockage|Octets|Average|Utilisation du stockage|Nœud|
|IOReadBytes|E/S de lecture d’octets|Octets|Total|E/S de lecture d’octets|Nœud|
|IOWriteBytes|E/S d’écriture d’octets|Octets|Total|E/S d’écriture d’octets|Nœud|
|ConnectionAccepted|Connexions acceptées|Count|Total|Connexions acceptées|Nœud|
|ConnectionHandled|Connexions gérées|Count|Total|Connexions gérées|Nœud|
|ConnectionActive|Connexions actives|Count|Average|Connexions actives|Nœud|
|RequestHandled|Demandes traitées|Count|Total|Demandes traitées|Nœud|
|ProcessedBlocks|Blocs traités|Count|Total|Blocs traités|Nœud|
|ProcessedTransactions|Transactions traitées|Count|Total|Transactions traitées|Nœud|
|PendingTransactions|Transactions en attente|Count|Average|Transactions en attente|Nœud|
|QueuedTransactions|Transactions en file d’attente|Count|Average|Transactions en file d’attente|Nœud|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|connectedclients|Clients connectés|Count|Maximale||ShardId|
|totalcommandsprocessed|Total des opérations|Count|Total||ShardId|
|cachehits|Présences dans le cache|Count|Total||ShardId|
|cachemisses|Absences dans le cache|Count|Total||ShardId|
|cachemissrate|Taux d’échec d’accès au cache|Pourcentage|cachemissrate||ShardId|
|getcommands|Gets|Count|Total||ShardId|
|setcommands|Jeux|Count|Total||ShardId|
|operationsPerSecond|Opérations par seconde|Count|Maximale||ShardId|
|evictedkeys|Clés exclues|Count|Total||ShardId|
|totalkeys|Nombre total de clés|Count|Maximale||ShardId|
|expiredkeys|Clés expirées|Count|Total||ShardId|
|usedmemory|Mémoire utilisée|Octets|Maximale||ShardId|
|usedmemorypercentage|Pourcentage de mémoire utilisé|Pourcentage|Maximale||ShardId|
|usedmemoryRss|Taille de la mémoire résidente utilisée|Octets|Maximale||ShardId|
|serverLoad|Charge du serveur|Pourcentage|Maximale||ShardId|
|cacheWrite|Cache d’écriture|BytesPerSecond|Maximale||ShardId|
|cacheRead|Lecture du cache|BytesPerSecond|Maximale||ShardId|
|percentProcessorTime|UC|Pourcentage|Maximale||ShardId|
|cacheLatency|Latence de cache en microsecondes (préversion)|Count|Average||ShardId|
|erreurs|Erreurs|Count|Maximale||ShardId,ErrorType|
|connectedclients0|Clients connectés (Shard 0)|Count|Maximale||None|
|totalcommandsprocessed0|Total des opérations (Shard 0)|Count|Total||None|
|cachehits0|Présences dans le cache (Shard 0)|Count|Total||None|
|cachemisses0|Absences dans le cache (Shard 0)|Count|Total||None|
|getcommands0|Gets (Shard 0)|Count|Total||None|
|setcommands0|Sets (Shard 0)|Count|Total||None|
|operationsPerSecond0|Opérations par seconde (Partition 0)|Count|Maximale||None|
|evictedkeys0|Clés exclues (Shard 0)|Count|Total||None|
|totalkeys0|Nombre total de clés (Shard 0)|Count|Maximale||None|
|expiredkeys0|Clés expirées (Shard 0)|Count|Total||None|
|usedmemory0|Mémoire utilisée (Shard 0)|Octets|Maximale||None|
|usedmemoryRss0|Mémoire résidente utilisée (Shard 0)|Octets|Maximale||None|
|serverLoad0|Charge du serveur (Shard 0)|Pourcentage|Maximale||None|
|cacheWrite0|Cache d’écriture (Shard 0)|BytesPerSecond|Maximale||None|
|cacheRead0|Cache de lecture (Shard 0)|BytesPerSecond|Maximale||None|
|percentProcessorTime0|UC (Shard 0)|Pourcentage|Maximale||None|
|connectedclients1|Clients connectés (Shard 1)|Count|Maximale||None|
|totalcommandsprocessed1|Total des opérations (Shard 1)|Count|Total||None|
|cachehits1|Présences dans le cache (Shard 1)|Count|Total||None|
|cachemisses1|Absences dans le cache (Shard 1)|Count|Total||None|
|getcommands1|Gets (Shard 1)|Count|Total||None|
|setcommands1|Sets (Shard 1)|Count|Total||None|
|operationsPerSecond1|Opérations par seconde (Partition 1)|Count|Maximale||None|
|evictedkeys1|Clés exclues (Shard 1)|Count|Total||None|
|totalkeys1|Nombre total de clés (Shard 1)|Count|Maximale||None|
|expiredkeys1|Clés expirées (Shard 1)|Count|Total||None|
|usedmemory1|Mémoire utilisée (Shard 1)|Octets|Maximale||None|
|usedmemoryRss1|Mémoire résidente utilisée (Shard 1)|Octets|Maximale||None|
|serverLoad1|Charge du serveur (Shard 1)|Pourcentage|Maximale||None|
|cacheWrite1|Cache d’écriture (Shard 1)|BytesPerSecond|Maximale||None|
|cacheRead1|Lecture du cache (Shard 1)|BytesPerSecond|Maximale||None|
|percentProcessorTime1|UC (Shard 1)|Pourcentage|Maximale||None|
|connectedclients2|Clients connectés (Shard 2)|Count|Maximale||None|
|totalcommandsprocessed2|Total des opérations (Shard 2)|Count|Total||None|
|cachehits2|Présences dans le cache (Shard 2)|Count|Total||None|
|cachemisses2|Absences dans le cache (Shard 2)|Count|Total||None|
|getcommands2|Gets (Shard 2)|Count|Total||None|
|setcommands2|Sets (Shard 2)|Count|Total||None|
|operationsPerSecond2|Opérations par seconde (Partition 2)|Count|Maximale||None|
|evictedkeys2|Clés exclues (Shard 2)|Count|Total||None|
|totalkeys2|Nombre total de clés (Shard 2)|Count|Maximale||None|
|expiredkeys2|Clés expirées (Shard 2)|Count|Total||None|
|usedmemory2|Mémoire utilisée (Shard 2)|Octets|Maximale||None|
|usedmemoryRss2|Mémoire résidente utilisée (Shard 2)|Octets|Maximale||None|
|serverLoad2|Charge du serveur (Shard 2)|Pourcentage|Maximale||None|
|cacheWrite2|Cache d’écriture (Shard 2)|BytesPerSecond|Maximale||None|
|cacheRead2|Lecture du cache (Shard 2)|BytesPerSecond|Maximale||None|
|percentProcessorTime2|UC (Shard 2)|Pourcentage|Maximale||None|
|connectedclients3|Clients connectés (Shard 3)|Count|Maximale||None|
|totalcommandsprocessed3|Total des opérations (Shard 3)|Count|Total||None|
|cachehits3|Présences dans le cache (Shard 3)|Count|Total||None|
|cachemisses3|Absences dans le cache (Shard 3)|Count|Total||None|
|getcommands3|Gets (Shard 3)|Count|Total||None|
|setcommands3|Sets (Shard 3)|Count|Total||None|
|operationsPerSecond3|Opérations par seconde (Partition 3)|Count|Maximale||None|
|evictedkeys3|Clés exclues (Shard 3)|Count|Total||None|
|totalkeys3|Nombre total de clés (Shard 3)|Count|Maximale||None|
|expiredkeys3|Clés expirées (Shard 3)|Count|Total||None|
|usedmemory3|Mémoire utilisée (Shard 3)|Octets|Maximale||None|
|usedmemoryRss3|Mémoire résidente utilisée (Shard 3)|Octets|Maximale||None|
|serverLoad3|Charge du serveur (Shard 3)|Pourcentage|Maximale||None|
|cacheWrite3|Cache d’écriture (Shard 3)|BytesPerSecond|Maximale||None|
|cacheRead3|Lecture du cache (Shard 3)|BytesPerSecond|Maximale||None|
|percentProcessorTime3|UC (Shard 3)|Pourcentage|Maximale||None|
|connectedclients4|Clients connectés (Shard 4)|Count|Maximale||None|
|totalcommandsprocessed4|Total des opérations (Shard 4)|Count|Total||None|
|cachehits4|Présences dans le cache (Shard 4)|Count|Total||None|
|cachemisses4|Absences dans le cache (Shard 4)|Count|Total||None|
|getcommands4|Gets (Shard 4)|Count|Total||None|
|setcommands4|Sets (Shard 4)|Count|Total||None|
|operationsPerSecond4|Opérations par seconde (Partition 4)|Count|Maximale||None|
|evictedkeys4|Clés exclues (Shard 4)|Count|Total||None|
|totalkeys4|Nombre total de clés (Shard 4)|Count|Maximale||None|
|expiredkeys4|Clés expirées (Shard 4)|Count|Total||None|
|usedmemory4|Mémoire utilisée (Shard 4)|Octets|Maximale||None|
|usedmemoryRss4|Mémoire résidente utilisée (Shard 4)|Octets|Maximale||None|
|serverLoad4|Charge du serveur (Shard 4)|Pourcentage|Maximale||None|
|cacheWrite4|Cache d’écriture (Shard 4)|BytesPerSecond|Maximale||None|
|cacheRead4|Lecture du cache (Shard 4)|BytesPerSecond|Maximale||None|
|percentProcessorTime4|UC (Shard 4)|Pourcentage|Maximale||None|
|connectedclients5|Clients connectés (Shard 5)|Count|Maximale||None|
|totalcommandsprocessed5|Total des opérations (Shard 5)|Count|Total||None|
|cachehits5|Présences dans le cache (Shard 5)|Count|Total||None|
|cachemisses5|Absences dans le cache (Shard 5)|Count|Total||None|
|getcommands5|Gets (Shard 5)|Count|Total||None|
|setcommands5|Sets (Shard 5)|Count|Total||None|
|operationsPerSecond5|Opérations par seconde (Partition 5)|Count|Maximale||None|
|evictedkeys5|Clés exclues (Shard 5)|Count|Total||None|
|totalkeys5|Nombre total de clés (Shard 5)|Count|Maximale||None|
|expiredkeys5|Clés expirées (Shard 5)|Count|Total||None|
|usedmemory5|Mémoire utilisée (Shard 5)|Octets|Maximale||None|
|usedmemoryRss5|Mémoire résidente utilisée (Shard 5)|Octets|Maximale||None|
|serverLoad5|Charge du serveur (Shard 5)|Pourcentage|Maximale||None|
|cacheWrite5|Cache d’écriture (Shard 5)|BytesPerSecond|Maximale||None|
|cacheRead5|Lecture du cache (Shard 5)|BytesPerSecond|Maximale||None|
|percentProcessorTime5|UC (Shard 5)|Pourcentage|Maximale||None|
|connectedclients6|Clients connectés (Shard 6)|Count|Maximale||None|
|totalcommandsprocessed6|Total des opérations (Shard 6)|Count|Total||None|
|cachehits6|Présences dans le cache (Shard 6)|Count|Total||None|
|cachemisses6|Absences dans le cache (Shard 6)|Count|Total||None|
|getcommands6|Gets (Shard 6)|Count|Total||None|
|setcommands6|Sets (Shard 6)|Count|Total||None|
|operationsPerSecond6|Opérations par seconde (Partition 6)|Count|Maximale||None|
|evictedkeys6|Clés exclues (Shard 6)|Count|Total||None|
|totalkeys6|Nombre total de clés (Shard 6)|Count|Maximale||None|
|expiredkeys6|Clés expirées (Shard 6)|Count|Total||None|
|usedmemory6|Mémoire utilisée (Shard 6)|Octets|Maximale||None|
|usedmemoryRss6|Mémoire résidente utilisée (Shard 6)|Octets|Maximale||None|
|serverLoad6|Charge du serveur (Shard 6)|Pourcentage|Maximale||None|
|cacheWrite6|Cache d’écriture (Shard 6)|BytesPerSecond|Maximale||None|
|cacheRead6|Lecture du cache (Shard 6)|BytesPerSecond|Maximale||None|
|percentProcessorTime6|UC (Shard 6)|Pourcentage|Maximale||None|
|connectedclients7|Clients connectés (Shard 7)|Count|Maximale||None|
|totalcommandsprocessed7|Total des opérations (Shard 7)|Count|Total||None|
|cachehits7|Présences dans le cache (Shard 7)|Count|Total||None|
|cachemisses7|Absences dans le cache (Shard 7)|Count|Total||None|
|getcommands7|Gets (Shard 7)|Count|Total||None|
|setcommands7|Sets (Shard 7)|Count|Total||None|
|operationsPerSecond7|Opérations par seconde (Partition 7)|Count|Maximale||None|
|evictedkeys7|Clés exclues (Shard 7)|Count|Total||None|
|totalkeys7|Nombre total de clés (Shard 7)|Count|Maximale||None|
|expiredkeys7|Clés expirées (Shard 7)|Count|Total||None|
|usedmemory7|Mémoire utilisée (Shard 7)|Octets|Maximale||None|
|usedmemoryRss7|Mémoire résidente utilisée (Shard 7)|Octets|Maximale||None|
|serverLoad7|Charge du serveur (Shard 7)|Pourcentage|Maximale||None|
|cacheWrite7|Cache d’écriture (Shard 7)|BytesPerSecond|Maximale||None|
|cacheRead7|Lecture du cache (Shard 7)|BytesPerSecond|Maximale||None|
|percentProcessorTime7|UC (Shard 7)|Pourcentage|Maximale||None|
|connectedclients8|Clients connectés (Shard 8)|Count|Maximale||None|
|totalcommandsprocessed8|Total des opérations (Shard 8)|Count|Total||None|
|cachehits8|Présences dans le cache (Shard 8)|Count|Total||None|
|cachemisses8|Absences dans le cache (Shard 8)|Count|Total||None|
|getcommands8|Gets (Shard 8)|Count|Total||None|
|setcommands8|Sets (Shard 8)|Count|Total||None|
|operationsPerSecond8|Opérations par seconde (Partition 8)|Count|Maximale||None|
|evictedkeys8|Clés exclues (Shard 8)|Count|Total||None|
|totalkeys8|Nombre total de clés (Shard 8)|Count|Maximale||None|
|expiredkeys8|Clés expirées (Shard 8)|Count|Total||None|
|usedmemory8|Mémoire utilisée (Shard 8)|Octets|Maximale||None|
|usedmemoryRss8|Mémoire résidente utilisée (Shard 8)|Octets|Maximale||None|
|serverLoad8|Charge du serveur (Shard 8)|Pourcentage|Maximale||None|
|cacheWrite8|Cache d’écriture (Shard 8)|BytesPerSecond|Maximale||None|
|cacheRead8|Lecture du cache (Shard 8)|BytesPerSecond|Maximale||None|
|percentProcessorTime8|UC (Shard 8)|Pourcentage|Maximale||None|
|connectedclients9|Clients connectés (Shard 9)|Count|Maximale||None|
|totalcommandsprocessed9|Total des opérations (Shard 9)|Count|Total||None|
|cachehits9|Présences dans le cache (Shard 9)|Count|Total||None|
|cachemisses9|Absences dans le cache (Shard 9)|Count|Total||None|
|getcommands9|Gets (Shard 9)|Count|Total||None|
|setcommands9|Sets (Shard 9)|Count|Total||None|
|operationsPerSecond9|Opérations par seconde (Partition 9)|Count|Maximale||None|
|evictedkeys9|Clés exclues (Shard 9)|Count|Total||None|
|totalkeys9|Nombre total de clés (Shard 9)|Count|Maximale||None|
|expiredkeys9|Clés expirées (Shard 9)|Count|Total||None|
|usedmemory9|Mémoire utilisée (Shard 9)|Octets|Maximale||None|
|usedmemoryRss9|Mémoire résidente utilisée (Shard 9)|Octets|Maximale||None|
|serverLoad9|Charge du serveur (Shard 9)|Pourcentage|Maximale||None|
|cacheWrite9|Cache d’écriture (Shard 9)|BytesPerSecond|Maximale||None|
|cacheRead9|Cache de lecture (Shard 9)|BytesPerSecond|Maximale||None|
|percentProcessorTime9|UC (Shard 9)|Pourcentage|Maximale||None|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Nombre de requêtes du pare-feu d’applications web|Count|Total|Nombre de requêtes clients traitées par le pare-feu d’applications web|PolicyName,RuleName,Action|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Average|Pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles.|None|
|Network In|Network In|Octets|Total|Nombre d’octets reçus sur toutes les interfaces réseau par les machines virtuelles (trafic entrant).|None|
|Network Out|Network Out|Octets|Total|Nombre d’octets envoyés sur toutes les interfaces réseau par les machines virtuelles (trafic sortant).|None|
|Disk Read Bytes/Sec|Lecture du disque|BytesPerSecond|Average|Moyenne d’octets lus à partir du disque pendant la période d’analyse.|None|
|Disk Write Bytes/Sec|Écriture sur le disque|BytesPerSecond|Average|Moyenne d’octets écrits sur le disque pendant la période d’analyse.|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|E/S de lecture disque par seconde.|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|E/S d’écriture sur disque par seconde.|None|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Average|Pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles.|RoleInstanceId|
|Network In|Network In|Octets|Total|Nombre d’octets reçus sur toutes les interfaces réseau par les machines virtuelles (trafic entrant).|RoleInstanceId|
|Network Out|Network Out|Octets|Total|Nombre d’octets envoyés sur toutes les interfaces réseau par les machines virtuelles (trafic sortant).|RoleInstanceId|
|Disk Read Bytes/Sec|Lecture du disque|BytesPerSecond|Average|Moyenne d’octets lus à partir du disque pendant la période d’analyse.|RoleInstanceId|
|Disk Write Bytes/Sec|Écriture sur le disque|BytesPerSecond|Average|Moyenne d’octets écrits sur le disque pendant la période d’analyse.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|E/S de lecture disque par seconde.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|E/S d’écriture sur disque par seconde.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Capacité utilisée|Octets|Average|Capacité de compte utilisée|None|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType,GeoType,ApiName,Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType,ApiName,Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType,ApiName,Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Average|Latence utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans SuccessE2ELatency.|GeoType,ApiName,Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Average|Latence de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType,ApiName,Authentication|
|Disponibilité|Disponibilité|Pourcentage|Average|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType,ApiName,Authentication|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Capacité d’objet blob|Octets|Average|Quantité de stockage utilisée par le service BLOB du compte de stockage, en octets.|BlobType,Tier|
|BlobCount|Nombre d’objets blob|Count|Average|Nombre d’objets blob dans le service BLOB du compte de stockage.|BlobType,Tier|
|ContainerCount|Nombre de conteneurs d’objets blob|Count|Average|Nombre de conteneurs d’objets blob dans le service BLOB du compte de stockage.|None|
|IndexCapacity|Capacité d'index|Octets|Average|Stockage utilisé par l'index (hiérarchique) d'ADLS Gen2 en octets.|None|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType,GeoType,ApiName,Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType,ApiName,Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType,ApiName,Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Average|Latence utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans SuccessE2ELatency.|GeoType,ApiName,Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Average|Latence de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType,ApiName,Authentication|
|Disponibilité|Disponibilité|Pourcentage|Average|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType,ApiName,Authentication|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Capacité de la table|Octets|Average|Quantité de stockage utilisée par le service de Table du compte de stockage, en octets.|None|
|TableCount|Nombre de tables|Count|Average|Nombre de tables dans le service de Table du compte de stockage.|None|
|TableEntityCount|Nombre d’entités de table|Count|Average|Nombre d’entités de table dans le service de Table du compte de stockage.|None|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType,GeoType,ApiName,Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType,ApiName,Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType,ApiName,Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Average|Latence utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans SuccessE2ELatency.|GeoType,ApiName,Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Average|Latence de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType,ApiName,Authentication|
|Disponibilité|Disponibilité|Pourcentage|Average|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType,ApiName,Authentication|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|Capacité de fichiers|Octets|Average|Quantité de stockage utilisée par le service de Fichier du compte de stockage, en octets.|FileShare|
|FileCount|Nombre de fichiers|Count|Average|Nombre de fichiers dans le service de Fichier du compte de stockage.|FileShare|
|FileShareCount|Nombre de partages de fichiers|Count|Average|Nombre de partage de fichiers dans le service de Fichier du compte de stockage.|None|
|FileShareSnapshotCount|Nombre d’instantanés de partage de fichiers|Count|Average|Nombre d’instantanés présents sur le partage dans le service Files du compte de stockage.|FileShare|
|FileShareSnapshotSize|Taille d’instantané de partage de fichiers|Octets|Average|Quantité de stockage utilisée par les instantanés dans le service Fichier du compte de stockage, en octets.|FileShare|
|FileShareQuota|Taille du quota de partage de fichiers|Octets|Average|Limite supérieure de la quantité de stockage pouvant être utilisée par le service Azure Files, en octets.|FileShare|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType,GeoType,ApiName,Authentication,FileShare|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType,ApiName,Authentication,FileShare|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType,ApiName,Authentication,FileShare|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Average|Latence utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans SuccessE2ELatency.|GeoType,ApiName,Authentication,FileShare|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Average|Latence de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType,ApiName,Authentication,FileShare|
|Disponibilité|Disponibilité|Pourcentage|Average|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType,ApiName,Authentication,FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Capacité de la file d’attente|Octets|Average|Quantité de stockage utilisée par le service de File d’attente du compte de stockage, en octets.|None|
|QueueCount|Nombre de files d’attente|Count|Average|Nombre de files d’attente dans le service de File d’attente du compte de stockage.|None|
|QueueMessageCount|Nombre de messages dans la file d’attente|Count|Average|Nombre approximatif de messages en file d’attente dans le service de File d’attente du compte de stockage.|None|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType,GeoType,ApiName,Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType,ApiName,Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType,ApiName,Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Average|Latence utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans SuccessE2ELatency.|GeoType,ApiName,Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Average|Latence de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType,ApiName,Authentication|
|Disponibilité|Disponibilité|Pourcentage|Average|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType,ApiName,Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalCalls|Nombre total d’appels|Count|Total|Nombre total d’appels.|ApiName,OperationName,Region|
|SuccessfulCalls|Appels réussis|Count|Total|Nombre d’appels réussis.|ApiName,OperationName,Region|
|TotalErrors|Nombre total d’erreurs|Count|Total|Nombre total d’appels avec réponse d’erreur (code de réponse HTTP : 4xx ou 5xx).|ApiName,OperationName,Region|
|BlockedCalls|Appels bloqués|Count|Total|Nombre d’appels ayant dépassé la limite de débit ou de quota.|ApiName,OperationName,Region|
|ServerErrors|Erreurs de serveur|Count|Total|Nombre d’appels avec erreur interne du service (code de réponse HTTP : 5xx).|ApiName,OperationName,Region|
|ClientErrors|Erreurs de client|Count|Total|Nombre d’appels avec erreur côté client (code de réponse HTTP : 4xx).|ApiName,OperationName,Region|
|DataIn|Données entrantes|Octets|Total|Taille des données entrantes en octets.|ApiName,OperationName,Region|
|DataOut|Données sortantes|Octets|Total|Taille des données sortantes en octets.|ApiName,OperationName,Region|
|Latence|Latence|Millisecondes|Average|Latence en millisecondes.|ApiName,OperationName,Region|
|CharactersTranslated|Caractères traduits|Count|Total|Nombre total de caractères dans la requête de texte entrante.|ApiName,OperationName,Region|
|CharactersTrained|Caractères formés|Count|Total|Nombre total de caractères formés.|ApiName,OperationName,Region|
|SpeechSessionDuration|Durée de la session vocale|Secondes|Total|Durée totale de la session vocale en secondes.|ApiName,OperationName,Region|
|TotalTransactions|Nombre total de transactions|Count|Total|Nombre total de transactions.|None|
|TotalTokenCalls|Total d’appels de jeton|Count|Total|Nombre total d’appels de jeton.|ApiName,OperationName,Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Average|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|None|
|Network In|Octets entrants réseau facturables (déprécié)|Octets|Total|Nombre d’octets facturables reçus sur toutes les interfaces réseau par la ou les machines virtuelles (trafic entrant) (déprécié)|None|
|Network Out|Octets sortants réseau facturables (déprécié)|Octets|Total|Nombre d’octets facturables envoyés sur toutes les interfaces réseau par la ou les machines virtuelles (trafic sortant) (déprécié)|None|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Octets lus à partir du disque pendant la période d’analyse|None|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Octets écrits sur le disque pendant la période d’analyse|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|E/S de lecture disque par seconde|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|E/S d’écriture disque par seconde|None|
|Crédits de processeurs restants|Crédits de processeurs restants|Count|Average|Nombre total de crédits pouvant être consommés|None|
|Crédits de processeur consommés|Crédits de processeur consommés|Count|Average|Nombre total de crédits consommés par la machine virtuelle|None|
|Octets lus/s par disque|Octets lus/s sur disque de données (déconseillé)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse|SlotId|
|Octets écrits/s par disque|Octets écrits/s sur disque de données (déconseillé)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse|SlotId|
|Opérations de lecture/s par disque|Opérations de lecture/s sur disque de données (déconseillé)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse|SlotId|
|Opérations d’écriture/s par disque|Opérations d’écriture/s sur disque de données (déconseillé)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse|SlotId|
|QD par disque|QD par disque de données (déconseillé)|Count|Average|Longueur de file d’attente (ou QD) du disque de données|SlotId|
|Octets lus/s sur système d’exploitation par disque|Octets lus/s sur système d'exploitation par disque (déconseillé)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Octets écrits/s sur système d’exploitation par disque|Octets écrits/s sur disque du système d'exploitation (déconseillé)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Opérations de lecture/s sur système d’exploitation par disque|Opérations de lecture/s sur disque du système d'exploitation (déconseillé)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Opérations d’écriture/s sur système d’exploitation par disque|Opérations d’écriture/s sur disque du système d'exploitation (déconseillé)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|QD sur système d’exploitation par disque|QD du disque du système d'exploitation (déconseillé)|Count|Average|Longueur de file d’attente (ou QD) du disque du système d’exploitation|None|
|Octets lus/s sur disque de données|Octets lus/s sur disque de données (préversion)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse|Numéro d'unité logique|
|Octets écrits/s sur disque de données|Octets/s écrits sur disque de données (préversion)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse|Numéro d'unité logique|
|Opérations de lecture/s sur disque de données|Opérations de lecture/s sur disque de données (préversion)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse|Numéro d'unité logique|
|Opérations d’écriture/s sur disque de données|Opérations d’écriture/s sur disque de données (préversion)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse|Numéro d'unité logique|
|Longueur de file d’attente du disque de données|Longueur de file d’attente du disque de données (préversion)|Count|Average|Longueur de file d’attente (ou QD) du disque de données|Numéro d'unité logique|
|Octets lus/s sur disque du système d’exploitation|Octets lus/s sur disque du système d’exploitation (préversion)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Octets/s écrits sur disque du système d’exploitation|Octets/s écrits sur disque du système d’exploitation (préversion)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Opérations de lecture/s sur disque du système d’exploitation|Opérations de lecture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Opérations d’écriture/s sur disque du système d’exploitation|Opérations d’écriture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Longueur de file d’attente du disque du système d’exploitation|Longueur de file d’attente du disque de données (préversion)|Count|Average|Longueur de file d’attente (ou QD) du disque du système d’exploitation|None|
|Flux entrants|Flux entrants|Count|Average|Les flux entrants correspondent aux flux actuels dans le sens entrant (trafic entrant dans la machine virtuelle)|None|
|Flux sortants|Flux sortants|Count|Average|Les flux sortants correspondent aux flux actuels dans le sens sortant (trafic sortant de la machine virtuelle)|None|
|Taux de création maximal de flux entrants|Taux de création maximal de flux entrants (préversion)|CountPerSecond|Average|Taux de création maximal de flux entrants (trafic entrant dans la machine virtuelle)|None|
|Taux de création maximal de flux sortants|Taux de création maximal de flux sortants (préversion)|CountPerSecond|Average|Taux de création maximal de flux sortants (trafic sortant de la machine virtuelle)|None|
|Accès en lecture dans le cache de disque de données Premium|Accès en lecture dans le cache de disque de données Premium (préversion)|Pourcentage|Average|Accès en lecture dans le cache de disque de données Premium|Numéro d'unité logique|
|Échec de lecture dans le cache de disque de données Premium|Échec de lecture dans le cache de disque de données Premium (préversion)|Pourcentage|Average|Échec de lecture dans le cache de disque de données Premium|Numéro d'unité logique|
|Accès en lecture dans le cache de disque OS Premium|Accès en lecture dans le cache de disque OS Premium (préversion)|Pourcentage|Average|Accès en lecture dans le cache de disque OS Premium|None|
|Échec de lecture dans le cache de disque OS Premium|Échec de lecture dans le cache de disque OS Premium (préversion)|Pourcentage|Average|Échec de lecture dans le cache de disque OS Premium|None|
|Octets entrants réseau totaux|Octets entrants réseau totaux|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|None|
|Octets sortants réseau totaux|Octets sortants réseau totaux|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Average|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|VMName|
|Network In|Octets entrants réseau facturables (déprécié)|Octets|Total|Nombre d’octets facturables reçus sur toutes les interfaces réseau par la ou les machines virtuelles (trafic entrant) (déprécié)|VMName|
|Network Out|Octets sortants réseau facturables (déprécié)|Octets|Total|Nombre d’octets facturables envoyés sur toutes les interfaces réseau par la ou les machines virtuelles (trafic sortant) (déprécié)|VMName|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Octets lus à partir du disque pendant la période d’analyse|VMName|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Octets écrits sur le disque pendant la période d’analyse|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|E/S de lecture disque par seconde|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|E/S d’écriture disque par seconde|VMName|
|Crédits de processeurs restants|Crédits de processeurs restants|Count|Average|Nombre total de crédits pouvant être consommés|None|
|Crédits de processeur consommés|Crédits de processeur consommés|Count|Average|Nombre total de crédits consommés par la machine virtuelle|None|
|Octets lus/s par disque|Octets lus/s sur disque de données (déconseillé)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse|SlotId|
|Octets écrits/s par disque|Octets écrits/s sur disque de données (déconseillé)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse|SlotId|
|Opérations de lecture/s par disque|Opérations de lecture/s sur disque de données (déconseillé)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse|SlotId|
|Opérations d’écriture/s par disque|Opérations d’écriture/s sur disque de données (déconseillé)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse|SlotId|
|QD par disque|QD par disque de données (déconseillé)|Count|Average|Longueur de file d’attente (ou QD) du disque de données|SlotId|
|Octets lus/s sur système d’exploitation par disque|Octets lus/s sur système d'exploitation par disque (déconseillé)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Octets écrits/s sur système d’exploitation par disque|Octets écrits/s sur disque du système d'exploitation (déconseillé)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Opérations de lecture/s sur système d’exploitation par disque|Opérations de lecture/s sur disque du système d'exploitation (déconseillé)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Opérations d’écriture/s sur système d’exploitation par disque|Opérations d’écriture/s sur disque du système d'exploitation (déconseillé)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|QD sur système d’exploitation par disque|QD du disque du système d'exploitation (déconseillé)|Count|Average|Longueur de file d’attente (ou QD) du disque du système d’exploitation|None|
|Octets lus/s sur disque de données|Octets lus/s sur disque de données (préversion)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse|LUN,VMName|
|Octets écrits/s sur disque de données|Octets/s écrits sur disque de données (préversion)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse|LUN,VMName|
|Opérations de lecture/s sur disque de données|Opérations de lecture/s sur disque de données (préversion)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse|LUN,VMName|
|Opérations d’écriture/s sur disque de données|Opérations d’écriture/s sur disque de données (préversion)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse|LUN,VMName|
|Longueur de file d’attente du disque de données|Longueur de file d’attente du disque de données (préversion)|Count|Average|Longueur de file d’attente (ou QD) du disque de données|LUN,VMName|
|Octets lus/s sur disque du système d’exploitation|Octets lus/s sur disque du système d’exploitation (préversion)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse du disque du système d’exploitation|VMName|
|Octets/s écrits sur disque du système d’exploitation|Octets/s écrits sur disque du système d’exploitation (préversion)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse du disque du système d’exploitation|VMName|
|Opérations de lecture/s sur disque du système d’exploitation|Opérations de lecture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|VMName|
|Opérations d’écriture/s sur disque du système d’exploitation|Opérations d’écriture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|VMName|
|Longueur de file d’attente du disque du système d’exploitation|Longueur de file d’attente du disque de données (préversion)|Count|Average|Longueur de file d’attente (ou QD) du disque du système d’exploitation|VMName|
|Flux entrants|Flux entrants|Count|Average|Les flux entrants correspondent aux flux actuels dans le sens entrant (trafic entrant dans la machine virtuelle)|VMName|
|Flux sortants|Flux sortants|Count|Average|Les flux sortants correspondent aux flux actuels dans le sens sortant (trafic sortant de la machine virtuelle)|VMName|
|Taux de création maximal de flux entrants|Taux de création maximal de flux entrants (préversion)|CountPerSecond|Average|Taux de création maximal de flux entrants (trafic entrant dans la machine virtuelle)|VMName|
|Taux de création maximal de flux sortants|Taux de création maximal de flux sortants (préversion)|CountPerSecond|Average|Taux de création maximal de flux sortants (trafic sortant de la machine virtuelle)|VMName|
|Accès en lecture dans le cache de disque de données Premium|Accès en lecture dans le cache de disque de données Premium (préversion)|Pourcentage|Average|Accès en lecture dans le cache de disque de données Premium|LUN,VMName|
|Échec de lecture dans le cache de disque de données Premium|Échec de lecture dans le cache de disque de données Premium (préversion)|Pourcentage|Average|Échec de lecture dans le cache de disque de données Premium|LUN,VMName|
|Accès en lecture dans le cache de disque OS Premium|Accès en lecture dans le cache de disque OS Premium (préversion)|Pourcentage|Average|Accès en lecture dans le cache de disque OS Premium|VMName|
|Échec de lecture dans le cache de disque OS Premium|Échec de lecture dans le cache de disque OS Premium (préversion)|Pourcentage|Average|Échec de lecture dans le cache de disque OS Premium|VMName|
|Octets entrants réseau totaux|Octets entrants réseau totaux|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|VMName|
|Octets sortants réseau totaux|Octets sortants réseau totaux|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Average|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|None|
|Network In|Octets entrants réseau facturables (déprécié)|Octets|Total|Nombre d’octets facturables reçus sur toutes les interfaces réseau par la ou les machines virtuelles (trafic entrant) (déprécié)|None|
|Network Out|Octets sortants réseau facturables (déprécié)|Octets|Total|Nombre d’octets facturables envoyés sur toutes les interfaces réseau par la ou les machines virtuelles (trafic sortant) (déprécié)|None|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Octets lus à partir du disque pendant la période d’analyse|None|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Octets écrits sur le disque pendant la période d’analyse|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|E/S de lecture disque par seconde|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|E/S d’écriture disque par seconde|None|
|Crédits de processeurs restants|Crédits de processeurs restants|Count|Average|Nombre total de crédits pouvant être consommés|None|
|Crédits de processeur consommés|Crédits de processeur consommés|Count|Average|Nombre total de crédits consommés par la machine virtuelle|None|
|Octets lus/s par disque|Octets lus/s sur disque de données (déconseillé)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse|SlotId|
|Octets écrits/s par disque|Octets écrits/s sur disque de données (déconseillé)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse|SlotId|
|Opérations de lecture/s par disque|Opérations de lecture/s sur disque de données (déconseillé)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse|SlotId|
|Opérations d’écriture/s par disque|Opérations d’écriture/s sur disque de données (déconseillé)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse|SlotId|
|QD par disque|QD par disque de données (déconseillé)|Count|Average|Longueur de file d’attente (ou QD) du disque de données|SlotId|
|Octets lus/s sur système d’exploitation par disque|Octets lus/s sur système d'exploitation par disque (déconseillé)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Octets écrits/s sur système d’exploitation par disque|Octets écrits/s sur disque du système d'exploitation (déconseillé)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Opérations de lecture/s sur système d’exploitation par disque|Opérations de lecture/s sur disque du système d'exploitation (déconseillé)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Opérations d’écriture/s sur système d’exploitation par disque|Opérations d’écriture/s sur disque du système d'exploitation (déconseillé)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|QD sur système d’exploitation par disque|QD du disque du système d'exploitation (déconseillé)|Count|Average|Longueur de file d’attente (ou QD) du disque du système d’exploitation|None|
|Octets lus/s sur disque de données|Octets lus/s sur disque de données (préversion)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse|Numéro d'unité logique|
|Octets écrits/s sur disque de données|Octets/s écrits sur disque de données (préversion)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse|Numéro d'unité logique|
|Opérations de lecture/s sur disque de données|Opérations de lecture/s sur disque de données (préversion)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse|Numéro d'unité logique|
|Opérations d’écriture/s sur disque de données|Opérations d’écriture/s sur disque de données (préversion)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse|Numéro d'unité logique|
|Longueur de file d’attente du disque de données|Longueur de file d’attente du disque de données (préversion)|Count|Average|Longueur de file d’attente (ou QD) du disque de données|Numéro d'unité logique|
|Octets lus/s sur disque du système d’exploitation|Octets lus/s sur disque du système d’exploitation (préversion)|CountPerSecond|Average|Octets/s lus sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Octets/s écrits sur disque du système d’exploitation|Octets/s écrits sur disque du système d’exploitation (préversion)|CountPerSecond|Average|Octets/s écrits sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Opérations de lecture/s sur disque du système d’exploitation|Opérations de lecture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Average|IOPS en lecture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Opérations d’écriture/s sur disque du système d’exploitation|Opérations d’écriture/s sur disque du système d’exploitation (préversion)|CountPerSecond|Average|IOPS en écriture effectuées sur un seul disque pendant la période d'analyse du disque du système d’exploitation|None|
|Longueur de file d’attente du disque du système d’exploitation|Longueur de file d’attente du disque de données (préversion)|Count|Average|Longueur de file d’attente (ou QD) du disque du système d’exploitation|None|
|Flux entrants|Flux entrants|Count|Average|Les flux entrants correspondent aux flux actuels dans le sens entrant (trafic entrant dans la machine virtuelle)|None|
|Flux sortants|Flux sortants|Count|Average|Les flux sortants correspondent aux flux actuels dans le sens sortant (trafic sortant de la machine virtuelle)|None|
|Taux de création maximal de flux entrants|Taux de création maximal de flux entrants (préversion)|CountPerSecond|Average|Taux de création maximal de flux entrants (trafic entrant dans la machine virtuelle)|None|
|Taux de création maximal de flux sortants|Taux de création maximal de flux sortants (préversion)|CountPerSecond|Average|Taux de création maximal de flux sortants (trafic sortant de la machine virtuelle)|None|
|Accès en lecture dans le cache de disque de données Premium|Accès en lecture dans le cache de disque de données Premium (préversion)|Pourcentage|Average|Accès en lecture dans le cache de disque de données Premium|Numéro d'unité logique|
|Échec de lecture dans le cache de disque de données Premium|Échec de lecture dans le cache de disque de données Premium (préversion)|Pourcentage|Average|Échec de lecture dans le cache de disque de données Premium|Numéro d'unité logique|
|Accès en lecture dans le cache de disque OS Premium|Accès en lecture dans le cache de disque OS Premium (préversion)|Pourcentage|Average|Accès en lecture dans le cache de disque OS Premium|None|
|Échec de lecture dans le cache de disque OS Premium|Échec de lecture dans le cache de disque OS Premium (préversion)|Pourcentage|Average|Échec de lecture dans le cache de disque OS Premium|None|
|Octets entrants réseau totaux|Octets entrants réseau totaux|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|None|
|Octets sortants réseau totaux|Octets sortants réseau totaux|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuUsage|Utilisation du processeur|Count|Average|Utilisation du processeur sur tous les cœurs en millicores.|containerName|
|MemoryUsage|Utilisation de la mémoire|Octets|Average|Utilisation de la mémoire totale en octets.|containerName|
|NetworkBytesReceivedPerSecond|Octets réseau reçus par seconde|Octets|Average|Octets réseau reçus par seconde.|None|
|NetworkBytesTransmittedPerSecond|Octets réseau transmis par seconde|Octets|Average|Octets réseau transmis par seconde.|None|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalPullCount|Nombre total d'extractions|Count|Average|Nombre total d'images extraites|None|
|SuccessfulPullCount|Nombre d'extractions réussies|Count|Average|Nombre d'images extraites réussies|None|
|TotalPushCount|Nombre total d'envois (push)|Count|Average|Nombre total d'images envoyées (push)|None|
|SuccessfulPushCount|Nombre d'envois (push) réussis|Count|Average|Nombre d'images envoyées (push) réussies|None|
|RunDuration|Durée d’exécution|Millisecondes|Total|Durée d’exécution en millisecondes|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Nombre total de cœurs d’unité centrale disponibles dans un cluster géré|Count|Average|Nombre total de cœurs d’unité centrale disponibles dans un cluster géré|None|
|kube_node_status_allocatable_memory_bytes|Quantité totale de mémoire disponible dans un cluster géré|Octets|Average|Quantité totale de mémoire disponible dans un cluster géré|None|
|kube_pod_status_ready|Nombre de pods en état Prêt|Count|Average|Nombre de pods en état Prêt|namespace,pod|
|kube_node_status_condition|États pour les différentes conditions de nœud|Count|Average|États pour les différentes conditions de nœud|condition,status,status2,node|
|kube_pod_status_phase|Nombre de pods par phase|Count|Average|Nombre de pods par phase|phase,namespace,pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SuccessfullRequests|Requêtes ayant réussi|Count|Total|Demandes réussies effectuées par le fournisseur personnalisé|HttpMethod,CallPath,StatusCode|
|FailedRequests|Demandes ayant échoué|Count|Total|Obtient les journaux disponibles pour les fournisseurs de ressources personnalisés|HttpMethod,CallPath,StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|NICReadThroughput|Débit de lecture (réseau)|BytesPerSecond|Average|Débit de lecture de l’interface réseau de l’appareil dans la période du rapport pour tous les volumes de la passerelle.|InstanceName|
|NICWriteThroughput|Débit d’écriture (réseau)|BytesPerSecond|Average|Débit d'écriture de l’interface réseau de l’appareil dans la période du rapport pour tous les volumes de la passerelle.|InstanceName|
|CloudReadThroughputPerShare|Débit de téléchargement cloud (partage)|BytesPerSecond|Average|Débit de téléchargement vers Azure à partir d’un partage pendant la période du rapport.|Partager|
|CloudUploadThroughputPerShare|Débit de chargement cloud (partage)|BytesPerSecond|Average|Débit de chargement vers Azure à partir d’un partage pendant la période du rapport.|Partager|
|BytesUploadedToCloudPerShare|Octets cloud chargés (partage)|Octets|Average|Nombre total d’octets chargés vers Azure à partir d’un partage pendant la période du rapport.|Partager|
|TotalCapacity|Capacité totale|Octets|Average|Capacité totale|None|
|AvailableCapacity|Capacité disponible|Octets|Average|Capacité disponible en octets pendant la période du rapport.|None|
|CloudUploadThroughput|Débit de chargement cloud|BytesPerSecond|Average|Débit de chargement cloud vers Azure pendant la période du rapport.|None|
|CloudReadThroughput|Débit de téléchargement cloud|BytesPerSecond|Average|Débit de téléchargement cloud vers Azure pendant la période du rapport.|None|
|BytesUploadedToCloud|Octets cloud chargés (appareil)|Octets|Average|Nombre total d’octets chargés vers Azure à partir d’un appareil pendant la période du rapport.|None|
|HyperVVirtualProcessorUtilization|Computing en périphérie - Pourcentage du processeur|Pourcentage|Average|Pourcentage d’utilisation du processeur|InstanceName|
|HyperVMemoryUtilization|Computing en périphérie - Utilisation de la mémoire|Pourcentage|Average|Quantité de RAM utilisée|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|FailedRuns|Exécutions échouées|Count|Total||pipelineName,activityName|
|SuccessfulRuns|Exécutions réussies|Count|Total||pipelineName,activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métriques d’exécutions de pipeline ayant échoué|Count|Total||FailureType,Name|
|PipelineSucceededRuns|Métriques d’exécutions de pipeline ayant abouti|Count|Total||FailureType,Name|
|PipelineCancelledRuns|Métriques d’exécutions de pipeline annulées|Count|Total||FailureType,Name|
|ActivityFailedRuns|Métriques d’exécutions d’activité ayant échoué|Count|Total||ActivityType,PipelineName,FailureType,Name|
|ActivitySucceededRuns|Métriques d’exécutions d’activité ayant abouti|Count|Total||ActivityType,PipelineName,FailureType,Name|
|ActivityCancelledRuns|Métriques d’exécutions d’activité annulées|Count|Total||ActivityType,PipelineName,FailureType,Name|
|TriggerFailedRuns|Métriques d’exécutions de déclencheur ayant échoué|Count|Total||Name,FailureType|
|TriggerSucceededRuns|Métriques d’exécutions de déclencheur ayant abouti|Count|Total||Name,FailureType|
|TriggerCancelledRuns|Métriques d’exécutions de déclencheur annulées|Count|Total||Name,FailureType|
|IntegrationRuntimeCpuPercentage|Utilisation du processeur du runtime d’intégration|Pourcentage|Average||IntegrationRuntimeName,NodeName|
|IntegrationRuntimeAvailableMemory|Mémoire disponible du runtime d’intégration|Octets|Average||IntegrationRuntimeName,NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Durée de la file d’attente du runtime d’intégration|Secondes|Average||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Longueur de la file d’attente du runtime d’intégration|Count|Average||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Nombre de nœuds disponibles pour le runtime d’intégration|Count|Average||IntegrationRuntimeName|
|MaxAllowedResourceCount|Nombre maximal d’entités autorisées|Count|Maximale||None|
|MaxAllowedFactorySizeInGbUnits|Taille maximale de fabrique autorisée (unité Go)|Count|Maximale||None|
|ResourceCount|Nombre total d’entités|Count|Maximale||None|
|FactorySizeInGbUnits|Taille de fabrique totale (unité Go)|Count|Maximale||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|JobEndedSuccess|Travaux réussis|Count|Total|Nombre de travaux réussis|None|
|JobEndedFailure|Travaux en échec|Count|Total|Nombre de travaux en échec|None|
|JobEndedCancelled|Travaux annulés|Count|Total|Nombre de travaux annulés|None|
|JobAUEndedSuccess|Durée AU de réussite|Secondes|Total|Durée AU totale des travaux réussis.|None|
|JobAUEndedFailure|Durée AU d’échec|Secondes|Total|Durée AU totale des travaux en échec|None|
|JobAUEndedCancelled|Durée AU d’annulation|Secondes|Total|Durée AU totale des travaux annulés|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TotalStorage|Stockage total|Octets|Maximale|Volume total de données stockées dans le compte.|None|
|DataWritten|Données écrites|Octets|Total|Volume total de données écrites dans le compte.|None|
|DataRead|Données lues|Octets|Total|Volume total de données lues à partir du compte.|None|
|WriteRequests|Demandes d’écriture|Count|Total|Nombre de demandes d’écriture de données sur le compte.|None|
|ReadRequests|Demandes de lecture|Count|Total|Nombre de demandes de lecture de données sur le compte.|None|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Average|Pourcentage d’UC|None|
|memory_percent|Pourcentage de mémoire|Pourcentage|Average|Pourcentage de mémoire|None|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Average|Pourcentage d’E/S|None|
|storage_percent|Pourcentage de stockage|Pourcentage|Average|Pourcentage de stockage|None|
|storage_used|Stockage utilisé|Octets|Average|Stockage utilisé|None|
|storage_limit|Limite de stockage|Octets|Maximale|Limite de stockage|None|
|serverlog_storage_percent|Pourcentage de stockage du journal du serveur|Pourcentage|Average|Pourcentage de stockage du journal du serveur|None|
|serverlog_storage_usage|Stockage du journal du serveur utilisé|Octets|Average|Stockage du journal du serveur utilisé|None|
|serverlog_storage_limit|Limite de stockage du journal du serveur|Octets|Average|Limite de stockage du journal du serveur|None|
|active_connections|Connexions actives|Count|Average|Connexions actives|None|
|connections_failed|Connexions ayant échoué|Count|Total|Connexions ayant échoué|None|
|seconds_behind_master|Décalage de la réplication en secondes|Count|Maximale|Décalage de la réplication en secondes|None|
|backup_storage_used|Stockage de sauvegarde utilisé|Octets|Average|Stockage de sauvegarde utilisé|None|
|network_bytes_egress|Network Out|Octets|Total|Sortie réseau entre connexions actives|None|
|network_bytes_ingress|Network In|Octets|Total|Entrée réseau entre connexions actives|None|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Average|Pourcentage d’UC|None|
|memory_percent|Pourcentage de mémoire|Pourcentage|Average|Pourcentage de mémoire|None|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Average|Pourcentage d’E/S|None|
|storage_percent|Pourcentage de stockage|Pourcentage|Average|Pourcentage de stockage|None|
|storage_used|Stockage utilisé|Octets|Average|Stockage utilisé|None|
|storage_limit|Limite de stockage|Octets|Maximale|Limite de stockage|None|
|serverlog_storage_percent|Pourcentage de stockage du journal du serveur|Pourcentage|Average|Pourcentage de stockage du journal du serveur|None|
|serverlog_storage_usage|Stockage du journal du serveur utilisé|Octets|Average|Stockage du journal du serveur utilisé|None|
|serverlog_storage_limit|Limite de stockage du journal du serveur|Octets|Maximale|Limite de stockage du journal du serveur|None|
|active_connections|Connexions actives|Count|Average|Connexions actives|None|
|connections_failed|Connexions ayant échoué|Count|Total|Connexions ayant échoué|None|
|seconds_behind_master|Décalage de la réplication en secondes|Count|Maximale|Décalage de la réplication en secondes|None|
|backup_storage_used|Stockage de sauvegarde utilisé|Octets|Average|Stockage de sauvegarde utilisé|None|
|network_bytes_egress|Network Out|Octets|Total|Sortie réseau entre connexions actives|None|
|network_bytes_ingress|Network In|Octets|Total|Entrée réseau entre connexions actives|None|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Average|Pourcentage d’UC|None|
|memory_percent|Pourcentage de mémoire|Pourcentage|Average|Pourcentage de mémoire|None|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Average|Pourcentage d’E/S|None|
|storage_percent|Pourcentage de stockage|Pourcentage|Average|Pourcentage de stockage|None|
|storage_used|Stockage utilisé|Octets|Average|Stockage utilisé|None|
|storage_limit|Limite de stockage|Octets|Maximale|Limite de stockage|None|
|serverlog_storage_percent|Pourcentage de stockage du journal du serveur|Pourcentage|Average|Pourcentage de stockage du journal du serveur|None|
|serverlog_storage_usage|Stockage du journal du serveur utilisé|Octets|Average|Stockage du journal du serveur utilisé|None|
|serverlog_storage_limit|Limite de stockage du journal du serveur|Octets|Maximale|Limite de stockage du journal du serveur|None|
|active_connections|Connexions actives|Count|Average|Connexions actives|None|
|connections_failed|Connexions ayant échoué|Count|Total|Connexions ayant échoué|None|
|backup_storage_used|Stockage de sauvegarde utilisé|Octets|Average|Stockage de sauvegarde utilisé|None|
|network_bytes_egress|Network Out|Octets|Total|Sortie réseau entre connexions actives|None|
|network_bytes_ingress|Network In|Octets|Total|Entrée réseau entre connexions actives|None|
|pg_replica_log_delay_in_seconds|Retard du réplica|Secondes|Maximale|Retard du réplica en secondes|None|
|pg_replica_log_delay_in_bytes|Retard maximum entre réplicas|Octets|Maximale|Retard en octets du réplica le plus en retard|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Average|Pourcentage d’UC|None|
|memory_percent|Pourcentage de mémoire|Pourcentage|Average|Pourcentage de mémoire|None|
|iops|E/S par seconde|Count|Average|Opérations d'E/S par seconde|None|
|storage_percent|Pourcentage de stockage|Pourcentage|Average|Pourcentage de stockage|None|
|storage_used|Stockage utilisé|Octets|Average|Stockage utilisé|None|
|active_connections|Connexions actives|Count|Average|Connexions actives|None|
|network_bytes_egress|Network Out|Octets|Total|Sortie réseau entre connexions actives|None|
|network_bytes_ingress|Network In|Octets|Total|Entrée réseau entre connexions actives|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentatives d’envoi de message de télémétrie|Count|Total|Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud à votre hub IoT|None|
|d2c.telemetry.ingress.success|Messages de télémétrie envoyés|Count|Total|Nombre de messages de télémétrie appareil vers cloud envoyés avec succès à votre hub IoT|None|
|c2d.commands.egress.complete.success|Remises de messages cloud vers appareil terminées|Count|Total|Nombre de remises de messages cloud vers appareil terminées avec succès par l’appareil|None|
|c2d.commands.egress.abandon.success|Messages cloud vers appareil abandonnés|Count|Total|Nombre de messages cloud vers appareil abandonnés par l’appareil|None|
|c2d.commands.egress.reject.success|Messages cloud vers appareil rejetés|Count|Total|Nombre de messages cloud vers appareil rejetés par l’appareil|None|
|C2DMessagesExpired|Messages cloud vers appareil ayant expiré (préversion)|Count|Total|Nombre de messages cloud vers appareil ayant expiré|None|
|devices.totalDevices|Nombre total d’appareils (déconseillé)|Count|Total|Nombre d’appareils enregistrés sur votre hub IoT|None|
|devices.connectedDevices.allProtocol|Appareils connectés (déconseillé) |Count|Total|Nombre d’appareils connectés à votre hub IoT|None|
|d2c.telemetry.egress.success|Routage : messages de télémétrie remis|Count|Total|Nombre de fois où des messages ont été correctement remis à tous les points de terminaison à l’aide du routage IoT Hub. Si un message est routé vers plusieurs points de terminaison, cette valeur augmente d’une unité pour chaque remise réussie. Si un message est routé plusieurs fois vers le même point de terminaison, cette valeur augmente d’une unité pour chaque remise réussie.|None|
|d2c.telemetry.egress.dropped|Routage : messages de télémétrie annulés |Count|Total|Nombre de fois où des messages ont été annulés par le routage IoT Hub en raison de points de terminaison morts. Cette valeur ne compte pas les messages remis à un itinéraire de secours, car les messages annulés n’y sont pas remis.|None|
|d2c.telemetry.egress.orphaned|Routage : messages de télémétrie orphelins |Count|Total|Nombre de fois où des messages ont été définis comme orphelins par le routage IoT Hub car ils ne correspondaient à aucune règle de routage (y compris la règle de secours). |None|
|d2c.telemetry.egress.invalid|Routage : messages de télémétrie incompatibles|Count|Total|Nombre de fois où le routage IoT Hub n’a pas réussi à remettre des messages en raison d’une incompatibilité avec le point de terminaison. Cette valeur n’inclut pas les nouvelles tentatives.|None|
|d2c.telemetry.egress.fallback|Routage : messages remis à l’itinéraire de secours|Count|Total|Nombre de fois où le routage IoT Hub a remis des messages au point de terminaison associé à l’itinéraire de secours.|None|
|d2c.endpoints.egress.eventHubs|Routage : messages remis à Event Hub|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison Event Hub.|None|
|d2c.endpoints.latency.eventHubs|Routage : latence des messages d’Event Hub|Millisecondes|Average|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et dans un point de terminaison Event Hub.|None|
|d2c.endpoints.egress.serviceBusQueues|Routage : messages remis à la file d’attente Service Bus|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages aux points de terminaison de file d’attente Service Bus.|None|
|d2c.endpoints.latency.serviceBusQueues|Routage : latence des messages de la file d’attente Service Bus|Millisecondes|Average|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de file d’attente Service Bus.|None|
|d2c.endpoints.egress.serviceBusTopics|Routage : messages remis à la rubrique Service Bus|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison de rubrique Service Bus.|None|
|d2c.endpoints.latency.serviceBusTopics|Routage : latence des messages de la rubrique Service Bus|Millisecondes|Average|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de rubrique Service Bus.|None|
|d2c.endpoints.egress.builtIn.events|Routage : messages remis à des messages/événements|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages au point de terminaison intégré (messages/événements).|None|
|d2c.endpoints.latency.builtIn.events|Routage : latence des messages de messages/d’événements|Millisecondes|Average|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans le point de terminaison intégré (messages/événements).|None|
|d2c.endpoints.egress.storage|Routage : messages remis au stockage|Count|Total|Nombre de fois où le routage IoT Hub a correctement remis des messages à des points de terminaison de stockage.|None|
|d2c.endpoints.latency.storage|Routage : latence des messages du stockage|Millisecondes|Average|Latence moyenne (en millisecondes) entre les entrées de messages vers IoT Hub et de télémétrie dans un point de terminaison de stockage.|None|
|d2c.endpoints.egress.storage.bytes|Routage : données remises au stockage|Octets|Total|Quantité de données (octets) que le routage IoT Hub a remis aux points de terminaison de stockage.|None|
|d2c.endpoints.egress.storage.blobs|Routage : objets blob remis au stockage|Count|Total|Nombre de fois où le routage IoT Hub a remis des objets blob à des points de terminaison de stockage.|None|
|EventGridDeliveries|Remises Event Grid (préversion)|Count|Total|Nombre d’événements IoT Hub publiés dans Event Grid. Utilisez la dimension Résultat pour le nombre de requêtes ayant réussi et ayant échoué. La dimension EventType représente le type de l’événement (https://aka.ms/ioteventgrid) ).|ResourceId,Result,EventType|
|EventGridLatency|Latence d’Event Grid (préversion)|Millisecondes|Average|Latence moyenne (en millisecondes) entre le moment où l’événement Iot Hub a été généré et le moment où l’événement a été publié dans Event Grid. Ce nombre est une moyenne de tous les types d’événement. Utilisez la dimension Type d’événement pour afficher la latence d’un type d’événement spécifique.|ResourceId,EventType|
|d2c.twin.read.success|Lectures de représentations réussies d’appareils|Count|Total|Total des lectures de représentations réussies initiées par un appareil.|None|
|d2c.twin.read.failure|Lectures de représentations d’appareils en échec|Count|Total|Total des lectures de représentations en échec initiées par un appareil.|None|
|d2c.twin.read.size|Taille de la réponse des lectures de représentations des appareils|Octets|Average|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un appareil.|None|
|d2c.twin.update.success|Mises à jour de représentations réussies d’appareils|Count|Total|Total des mises à jour de représentations réussies initiées par un appareil.|None|
|d2c.twin.update.failure|Mises à jour de représentations d’appareils en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un appareil.|None|
|d2c.twin.update.size|Taille des mises à jour de représentations d’appareils|Octets|Average|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un appareil.|None|
|c2d.methods.success|Appels de méthode directe réussis|Count|Total|Total des appels de méthode directe réussis.|None|
|c2d.methods.failure|Appels de méthode directe en échec|Count|Total|Total des appels de méthode directe en échec.|None|
|c2d.methods.requestSize|Taille de demande des appels de méthode directe|Octets|Average|Moyenne, minimum et maximum de toutes les demandes de méthode directe réussies.|None|
|c2d.methods.responseSize|Taille de réponse des appels de méthode directe|Octets|Average|Moyenne, minimum et maximum de toutes les réponses de méthode directe réussies.|None|
|c2d.twin.read.success|Lectures de représentations réussies de serveur principal|Count|Total|Total des lectures de représentations réussies initiées par un serveur principal.|None|
|c2d.twin.read.failure|Lectures de représentations de serveur principal en échec|Count|Total|Total des lectures de représentations en échec initiées par un serveur principal.|None|
|c2d.twin.read.size|Taille de la réponse des lectures de représentations de serveur principal|Octets|Average|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un serveur principal.|None|
|c2d.twin.update.success|Mises à jour de représentations réussies de serveur principal|Count|Total|Total des mises à jour de représentations réussies initiées par un serveur principal.|None|
|c2d.twin.update.failure|Mises à jour de représentations de serveur principal en échec|Count|Total|Total des mises à jour de représentations en échec initiées par un serveur principal.|None|
|c2d.twin.update.size|Taille des mises à jour de représentations de serveur principal|Octets|Average|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un serveur principal.|None|
|twinQueries.success|Requêtes de représentations réussies|Count|Total|Total des requêtes de représentations réussies.|None|
|twinQueries.failure|Requêtes de représentations en échec|Count|Total|Total des requêtes de représentations en échec.|None|
|twinQueries.resultSize|Taille du résultat des requêtes de représentations|Octets|Average|Moyenne, minimum et maximum de la taille du résultat de toutes les requêtes de représentations réussies.|None|
|jobs.createTwinUpdateJob.success|Créations réussies des travaux de mises à jour de représentations|Count|Total|Total des créations réussies de travaux de mises à jour de représentations.|None|
|jobs.createTwinUpdateJob.failure|Créations des travaux de mises à jour de représentations en échec|Count|Total|Total des créations en échec des travaux de mises à jour de représentations.|None|
|jobs.createDirectMethodJob.success|Créations réussies des travaux d’appel de méthode|Count|Total|Total des créations réussies des travaux d’appel de méthode directe.|None|
|jobs.createDirectMethodJob.failure|Créations des travaux d’appel de méthode en échec|Count|Total|Total des créations en échec des travaux d’appel de méthode directe.|None|
|jobs.listJobs.success|Appels réussis pour répertorier les travaux|Count|Total|Total des appels réussis pour répertorier les travaux.|None|
|jobs.listJobs.failure|Appels en échec pour répertorier les travaux|Count|Total|Total des appels en échec pour répertorier les travaux.|None|
|jobs.cancelJob.success|Annulations de travaux réussies|Count|Total|Total des appels réussis pour annuler un travail.|None|
|jobs.cancelJob.failure|Annulations de travaux en échec|Count|Total|Total des appels en échec pour annuler un travail.|None|
|jobs.queryJobs.success|Requêtes de travaux réussies|Count|Total|Total des appels réussis pour interroger les travaux.|None|
|jobs.queryJobs.failure|Requêtes de travaux en échec|Count|Total|Total des appels en échec pour interroger les travaux.|None|
|jobs.completed|Travaux terminés|Count|Total|Total des travaux terminés.|None|
|jobs.failed|Travaux en échec|Count|Total|Total des travaux en échec.|None|
|d2c.telemetry.ingress.sendThrottle|Nombre d’erreurs de limitation|Count|Total|Nombre d’erreurs de limitation causées par des limitations de débit d’appareil|None|
|dailyMessageQuotaUsed|Nombre total de messages utilisés|Count|Average|Nombre total de messages utilisés aujourd'hui|None|
|deviceDataUsage|Utilisation totale des données d’appareil|Octets|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|None|
|deviceDataUsageV2|Utilisation totale des données d’appareil (préversion)|Octets|Total|Nombre d’octets transférés vers et depuis tous les appareils connectés à IotHub|None|
|totalDeviceCount|Nombre total d’appareils (préversion)|Count|Average|Nombre d’appareils enregistrés sur votre hub IoT|None|
|connectedDeviceCount|Appareils connectés (préversion)|Count|Average|Nombre d’appareils connectés à votre hub IoT|None|
|configurations|Métriques de configuration|Count|Total|Métriques pour les opérations de configuration|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentatives d’enregistrement|Count|Total|Nombre d’inscriptions d’appareils tentées|ProvisioningServiceName,IotHubName,Status|
|DeviceAssignments|Appareils attribués|Count|Total|Nombre d’appareils affectés à un hub IoT|ProvisioningServiceName,IotHubName|
|AttestationAttempts|Tentatives d’attestation|Count|Total|Nombre d’attestations d’appareils tentées|ProvisioningServiceName,Status,Protocol|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|AddRegion|Région ajoutée|Count|Count|Région ajoutée|Région|
|AvailableStorage|Stockage disponible|Octets|Total|Stockage total disponible signalé à une granularité de 5 minutes|CollectionName,DatabaseName,Region|
|CassandraConnectionClosures|Fermetures de connexion Cassandra|Count|Total|Nombre de connexions de Cassandra fermées, signalées à une granularité d'une minute|APIType,Region,ClosureReason|
|CassandraRequestCharges|Frais de requête Cassandra|Count|Total|Unités de requête consommées pour les requêtes Cassandra effectuées|APIType,DatabaseName,CollectionName,Region,OperationType,ResourceType|
|CassandraRequests|Requêtes Cassandra|Count|Count|Nombre de requêtes Cassandra effectuées|APIType,DatabaseName,CollectionName,Region,OperationType,ResourceType,ErrorCode|
|CreateAccount|Compte créé|Count|Count|Compte créé|None|
|DataUsage|Utilisation des données|Octets|Total|Utilisation totale des données signalée à une granularité de 5 minutes|CollectionName,DatabaseName,Region|
|DeleteAccount|Compte supprimé|Count|Count|Compte supprimé|None|
|DocumentCount|Nombre de documents|Count|Total|Nombre total de documents signalé à une granularité de 5 minutes|CollectionName,DatabaseName,Region|
|DocumentQuota|Quota de document|Octets|Total|Quota de stockage total signalé à une granularité de 5 minutes|CollectionName,DatabaseName,Region|
|IndexUsage|Utilisation d'index|Octets|Total|Utilisation d’index totale signalée à une granularité de 5 minutes|CollectionName,DatabaseName,Region|
|MetadataRequests|Demandes de métadonnées|Count|Count|Nombre de demandes de métadonnées. Cosmos DB gère la collection des métadonnées système pour chaque compte, ce qui vous permet d’énumérer les collections, les bases de données, etc., ainsi que leur configuration, et ce gratuitement.|DatabaseName,CollectionName,Region,StatusCode,Role|
|MongoRequestCharge|Frais des requêtes Mongo|Count|Total|Unités de requête Mongo consommées|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|MongoRequests|Requêtes Mongo|Count|Count|Nombre de requêtes Mongo effectuées|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|MongoRequestsCount|Taux de demandes Mongo|CountPerSecond|Average|Nombre de demandes Mongo par seconde|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|MongoRequestsDelete|Taux de requête de suppression Mongo|CountPerSecond|Average|Demandes de suppression Mongo par seconde|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|MongoRequestsInsert|Taux de requête d’insertion Mongo|CountPerSecond|Average|Nombre d’insertions Mongo par seconde|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|MongoRequestsQuery|Taux de requêtes d’interrogation Mongo|CountPerSecond|Average|Demandes de requête Mongo par seconde|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|MongoRequestsUpdate|Taux de demande de mise à jour Mongo|CountPerSecond|Average|Demandes de mise à jour Mongo par seconde|DatabaseName,CollectionName,Region,CommandName,ErrorCode|
|ProvisionedThroughput|Débit approvisionné|Count|Maximale|Débit approvisionné|DatabaseName,CollectionName|
|RegionFailover|Région basculée|Count|Count|Région basculée|None|
|RemoveRegion|Région supprimée|Count|Count|Région supprimée|Région|
|ReplicationLatency|Latence de réplication P99|Millisecondes|Average|Latence de réplication P99 des régions source et cible pour le compte géolocalisé|SourceRegion,TargetRegion|
|ServiceAvailability|Disponibilité des services|Pourcentage|Average|Disponibilité des requêtes de compte à une granularité d’une heure, d'un jour ou d'un mois|None|
|TotalRequestUnits|Unités de requête totales|Count|Total|Unités de requête consommées|DatabaseName,CollectionName,Region,StatusCode,OperationType|
|TotalRequests|Total de requêtes|Count|Count|Nombre de requêtes effectuées|DatabaseName,CollectionName,Region,StatusCode,OperationType|
|UpdateAccountKeys|Clés de compte mises à jour|Count|Count|Clés de compte mises à jour|KeyType|
|UpdateAccountNetworkSettings|Paramètres réseau de compte mis à jour|Count|Count|Paramètres réseau de compte mis à jour|None|
|UpdateAccountReplicationSettings|Paramètres de réplication de compte mis à jour|Count|Count|Paramètres de réplication de compte mis à jour|None|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TransactionCount|Nombre de transactions|Count|Count|Nombre total de transactions|TransactionCount|
|SuccessCount|Nombre de succès|Count|Count|Nombre de transactions réussies|SuccessCount|
|FailureCount|Nombre de défaillances|Count|Count|Nombre de transactions non réussies|FailureCount|
|SuccessLatency|Latence de réussite|Millisecondes|Average|Latence des transactions réussies|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|Rubrique|
|PublishFailCount|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|Topic,ErrorType,Error|
|PublishSuccessLatencyInMs|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|None|
|MatchedEventCount|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Topic,EventSubscriptionName,DomainEventSubscriptionName,Error,ErrorType|
|DeliverySuccessCount|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|Topic,EventSubscriptionName,DomainEventSubscriptionName|
|DroppedEventCount|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|Topic,EventSubscriptionName,DomainEventSubscriptionName,DropReason|
|DeadLetteredCount|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|Topic,EventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|None|
|PublishFailCount|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType,Error|
|UnmatchedEventCount|Événements sans correspondance|Count|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|None|
|PublishSuccessLatencyInMs|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|None|
|MatchedEventCount|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|EventSubscriptionName|
|DeliveryAttemptFailCount|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error,ErrorType,EventSubscriptionName|
|DeliverySuccessCount|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|EventSubscriptionName|
|DestinationProcessingDurationInMs|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|EventSubscriptionName|
|DroppedEventCount|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|DropReason,EventSubscriptionName|
|DeadLetteredCount|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|MatchedEventCount|Événements correspondants|Count|Total|Nombre total d’événements correspondant à cet abonnement aux événements|None|
|DeliveryAttemptFailCount|Événements d’échec de la remise|Count|Total|Nombre total d’événements ayant échoué dans la remise à cet abonnement aux événements|Error,ErrorType|
|DeliverySuccessCount|Événements remis|Count|Total|Nombre total d’événements remis à cet abonnement aux événements|None|
|DestinationProcessingDurationInMs|Durée du traitement de la destination|Millisecondes|Average|Durée du traitement de la destination en millisecondes|None|
|DroppedEventCount|Événements annulés|Count|Total|Nombre total d’événements annulés correspondant à cet abonnement aux événements|DropReason|
|DeadLetteredCount|Événements de lettres mortes|Count|Total|Nombre total d’événements de lettres mortes correspondant à cet abonnement aux événements|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Événements publiés|Count|Total|Nombre total d’événements publiés dans cette rubrique|None|
|PublishFailCount|Événements d'échec de la publication|Count|Total|Nombre total d’événements ayant échoué à publier dans cette rubrique|ErrorType,Error|
|UnmatchedEventCount|Événements sans correspondance|Count|Total|Nombre total d’événements ne correspondant à aucun des abonnements aux événements pour cette rubrique|None|
|PublishSuccessLatencyInMs|Latence de réussite de la publication|Millisecondes|Total|Latence de réussite de la publication en millisecondes|None|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Requêtes ayant réussi|Count|Total|Requête réussies pour Microsoft.EventHub.|EntityName,OperationResult|
|ServerErrors|Erreurs de serveur.|Count|Total|Erreurs de serveur pour Microsoft.EventHub.|EntityName,OperationResult|
|UserErrors|Erreurs d’utilisateur.|Count|Total|Erreurs d’utilisateur pour Microsoft.EventHub.|EntityName,OperationResult|
|QuotaExceededErrors|Erreurs de dépassement de quota.|Count|Total|Erreurs de dépassement de quota pour Microsoft.EventHub.|EntityName,OperationResult|
|ThrottledRequests|Demandes limitées.|Count|Total|Demandes limitées pour Microsoft.EventHub.|EntityName,OperationResult|
|IncomingRequests|Demandes entrantes|Count|Total|Demandes entrantes pour Microsoft.EventHub.|EntityName|
|IncomingMessages|Messages entrants|Count|Total|Messages entrants pour Microsoft.EventHub.|EntityName|
|OutgoingMessages|Messages sortants|Count|Total|Messages sortants pour Microsoft.EventHub.|EntityName|
|IncomingBytes|Octets entrants.|Octets|Total|Octets entrants pour Microsoft.EventHub.|EntityName|
|OutgoingBytes|Octets sortants.|Octets|Total|Octets sortants pour Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Nombre total de connexions actives pour Microsoft.EventHub.|None|
|ConnectionsOpened|Connexions ouvertes.|Count|Average|Connexions ouvertes pour Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connexions fermées.|Count|Average|Connexions fermées pour Microsoft.EventHub.|EntityName|
|CaptureBacklog|Backlog des captures.|Count|Total|Backlog des captures de Microsoft.EventHub.|EntityName|
|CapturedMessages|Messages capturés.|Count|Total|Messages capturés pour Microsoft.EventHub.|EntityName|
|CapturedBytes|Octets capturés.|Octets|Total|Octets capturés pour Microsoft.EventHub.|EntityName|
|Taille|Taille|Octets|Average|Taille d’un EventHub en octets.|EntityName|
|INREQS|Requêtes entrantes (déconseillé)|Count|Total|Nombre total des requêtes d’envoi entrantes pour un espace de noms (déconseillé)|None|
|SUCCREQ|Requêtes réussies (déconseillé)|Count|Total|Nombre total de requêtes réussies pour un espace de noms (déconseillé)|None|
|FAILREQ|Requêtes non réussies (déconseillé)|Count|Total|Nombre total de requêtes non réussies pour un espace de noms (déconseillé)|None|
|SVRBSY|Erreurs de serveur occupé (déconseillé)|Count|Total|Nombre total d’erreurs de serveur occupé pour un espace de noms (déconseillé)|None|
|INTERR|Erreurs internes du serveur (déconseillé)|Count|Total|Nombre total d’erreurs internes du serveur pour un espace de noms (déconseillé)|None|
|MISCERR|Autres erreurs (déconseillé)|Count|Total|Nombre total de requêtes non réussies pour un espace de noms (déconseillé)|None|
|INMSGS|Messages entrants (obsolète) (déprécié)|Count|Total|Nombre total de messages entrants pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Messages entrants (déconseillé)|None|
|EHINMSGS|Messages entrants (déconseillé)|Count|Total|Nombre total de messages entrants pour un espace de noms (déconseillé)|None|
|OUTMSGS|Messages sortants (obsolète) (déprécié)|Count|Total|Nombre total de messages sortants pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Messages sortants (déconseillé)|None|
|EHOUTMSGS|Messages sortants (déconseillé)|Count|Total|Nombre total de messages sortants pour un espace de noms (déconseillé)|None|
|EHINMBS|Octets entrants (obsolète) (déprécié)|Octets|Total|Débit de messages entrants Event Hub pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Octets entrants (déconseillé)|None|
|EHINBYTES|Octets entrants (déconseillé)|Octets|Total|Débit de messages entrants Event Hub pour un espace de noms (déconseillé)|None|
|EHOUTMBS|Octets sortants (obsolète) (déprécié)|Octets|Total|Débit de messages sortants Event Hub pour un espace de noms. Cette métrique est déconseillée. Utilisez plutôt la métrique Octets sortants (déconseillé)|None|
|EHOUTBYTES|Octets sortants (déconseillé)|Octets|Total|Débit de messages sortants Event Hub pour un espace de noms (déconseillé)|None|
|EHABL|Archiver les messages de backlog (déconseillé)|Count|Total|Messages archivés Event Hub dans le backlog pour un espace de noms (déconseillé)|None|
|EHAMSGS|Messages archivés (déconseillé)|Count|Total|Messages archivés Event Hub pour un espace de noms (déconseillé)|None|
|EHAMBS|Archiver le débit des messages (déconseillé)|Octets|Total|Débit de messages archivés Event Hub dans un espace de noms (déconseillé)|None|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Requêtes ayant réussi|Count|Total|Requête réussies pour Microsoft.EventHub.|OperationResult|
|ServerErrors|Erreurs de serveur.|Count|Total|Erreurs de serveur pour Microsoft.EventHub.|OperationResult|
|UserErrors|Erreurs d’utilisateur.|Count|Total|Erreurs d’utilisateur pour Microsoft.EventHub.|OperationResult|
|QuotaExceededErrors|Erreurs de dépassement de quota.|Count|Total|Erreurs de dépassement de quota pour Microsoft.EventHub.|OperationResult|
|ThrottledRequests|Demandes limitées.|Count|Total|Demandes limitées pour Microsoft.EventHub.|OperationResult|
|IncomingRequests|Demandes entrantes|Count|Total|Demandes entrantes pour Microsoft.EventHub.|None|
|IncomingMessages|Messages entrants|Count|Total|Messages entrants pour Microsoft.EventHub.|None|
|OutgoingMessages|Messages sortants|Count|Total|Messages sortants pour Microsoft.EventHub.|None|
|IncomingBytes|Octets entrants.|Octets|Total|Octets entrants pour Microsoft.EventHub.|None|
|OutgoingBytes|Octets sortants.|Octets|Total|Octets sortants pour Microsoft.EventHub.|None|
|ActiveConnections|ActiveConnections|Count|Average|Nombre total de connexions actives pour Microsoft.EventHub.|None|
|ConnectionsOpened|Connexions ouvertes.|Count|Average|Connexions ouvertes pour Microsoft.EventHub.|None|
|ConnectionsClosed|Connexions fermées.|Count|Average|Connexions fermées pour Microsoft.EventHub.|None|
|CaptureBacklog|Backlog des captures.|Count|Total|Backlog des captures de Microsoft.EventHub.|None|
|CapturedMessages|Messages capturés.|Count|Total|Messages capturés pour Microsoft.EventHub.|None|
|CapturedBytes|Octets capturés.|Octets|Total|Octets capturés pour Microsoft.EventHub.|None|
|UC|UC|Pourcentage|Maximale|Utilisation de l’UC en pourcentage du cluster Event Hub|Role|
|AvailableMemory|Mémoire disponible|Pourcentage|Maximale|Mémoire disponible pour le cluster Event Hub, en pourcentage de la mémoire totale.|Role|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|GatewayRequests|Demandes de la passerelle|Count|Total|Nombre de demandes de la passerelle|httpStatus|
|CategorizedGatewayRequests|Demandes de la passerelle par catégorie|Count|Total|Nombre de demandes de la passerelle par catégories (1xx/2xx/3xx/4xx/5xx)|httpStatus|
|NumActiveWorkers|Nombre de collaborateurs actifs|Count|Maximale|Nombre de collaborateurs actifs|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ObservedMetricValue|Valeur de métrique observée|Count|Average|Valeur calculée par mise à l’échelle automatique lors de l’exécution|MetricTriggerSource|
|MetricThreshold|Seuil de métrique|Count|Average|Seuil de mise à l’échelle automatique configurée lors de l’exécution de la mise à l’échelle automatique.|MetricTriggerRule|
|ObservedCapacity|Capacité observée|Count|Average|Capacité envoyée à la mise à l’échelle automatique lors de l’exécution.|None|
|ScaleActionsInitiated|Actions de mise à l’échelle initiées|Count|Total|Direction de l’opération de mise à l’échelle.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilité|Pourcentage|Average|Pourcentage de tests de disponibilité terminés|availabilityResult/name,availabilityResult/location|
|availabilityResults/count|Tests de disponibilité|Count|Count|Nombre de tests de disponibilité|availabilityResult/name,availabilityResult/location,availabilityResult/success|
|availabilityResults/duration|Durée du test de disponibilité|Millisecondes|Average|Durée du test de disponibilité|availabilityResult/name,availabilityResult/location,availabilityResult/success|
|browserTimings/networkDuration|Temps de connexion au réseau pour le chargement de page|Millisecondes|Average|Temps écoulé entre la requête de l’utilisateur et la connexion réseau. Inclut la connexion de recherche DNS et de transport.|None|
|browserTimings/processingDuration|Temps de traitement du client|Millisecondes|Average|Temps écoulé entre la réception du dernier octet d’un document et le chargement du modèle DOM. Les demandes asynchrones peuvent encore être en cours de traitement.|None|
|browserTimings/receiveDuration|Temps de réception de réponse|Millisecondes|Average|Temps écoulé entre le premier et le dernier octet, ou jusqu’à la déconnexion.|None|
|browserTimings/sendDuration|Temps d’envoi de demande|Millisecondes|Average|Temps écoulé entre la connexion réseau et la réception du premier octet.|None|
|browserTimings/totalDuration|Temps de chargement de la page de navigateur|Millisecondes|Average|Temps s’écoulant entre la demande de l’utilisateur et le chargement du DOM, des feuilles de style, des scripts et des images.|None|
|dependencies/count|Appels de dépendance|Count|Count|Nombre d’appels effectués par l’application à des ressources externes.|dependency/type,dependency/performanceBucket,dependency/success,dependency/target,operation/synthetic,cloud/roleInstance,cloud/roleName|
|dependencies/duration|Durée de la dépendance|Millisecondes|Average|Durée des appels effectués par l’application aux ressources externes.|dependency/type,dependency/performanceBucket,dependency/success,dependency/target,operation/synthetic,cloud/roleInstance,cloud/roleName|
|dependencies/failed|Échecs d'appel de dépendance|Count|Count|Nombre d’échecs des appels de dépendance effectués par l’application à des ressources externes.|dependency/type,dependency/performanceBucket,dependency/success,dependency/target,operation/synthetic,cloud/roleInstance,cloud/roleName|
|pageViews/count|Affichages de page|Count|Count|Nombre de pages consultées.|operation/synthetic,cloud/roleName|
|pageViews/duration|Temps de chargement de la page consultée|Millisecondes|Average|Temps de chargement de la page consultée|operation/synthetic,cloud/roleName|
|performanceCounters/requestExecutionTime|Durée d’exécution de la requête HTTP|Millisecondes|Average|Durée d’exécution de la demande la plus récente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Requêtes HTTP dans la file d’attente de l’application|Count|Average|Longueur de la file d'attente des demandes d'application.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Taux de requêtes HTTP|CountPerSecond|Average|Taux par seconde de l'ensemble des demandes à l'application provenant d'ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Taux d’exceptions|CountPerSecond|Average|Nombre d’exceptions prises en charge et non prises en charge signalées à Windows, notamment les exceptions .NET et les exceptions non managées converties en exceptions .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Taux d’E/S du processus|BytesPerSecond|Average|Nombre total d’octets par seconde lus et écrits sur des fichiers, un réseau et des appareils.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Processeur de processus|Pourcentage|Average|Temps écoulé en pourcentage que tous les threads de processus a passé à utiliser le processeur pour exécuter des instructions. Il peut varier de 0 à 100. Cette métrique indique les performances du processus w3wp uniquement.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Temps processeur|Pourcentage|Average|Temps en pourcentage que le processus a passé dans des threads actifs.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Mémoire disponible|Octets|Average|Mémoire physique prête à être allouée immédiatement pour un processus ou pour le système.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Octets privés du processus|Octets|Average|Mémoire allouée exclusivement aux processus de l’application analysée.|cloud/roleInstance|
|requests/duration|Temps de réponse du serveur|Millisecondes|Average|Temps écoulé entre la réception d'une requête HTTP et la fin de l'envoi de la réponse.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|requests/count|Requêtes serveur|Count|Count|Nombre de requêtes HTTP terminées.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|requests/failed|Demandes ayant échoué|Count|Count|Nombre de requêtes HTTP marquées comme ayant échoué. Dans la plupart des cas, il s’agit de requêtes avec un code de réponse >= 400 et différent de 401.|request/performanceBucket,request/resultCode,request/success,operation/synthetic,cloud/roleInstance,cloud/roleName|
|requests/taux|Taux de requêtes du serveur|CountPerSecond|Average|Taux de requêtes du serveur par seconde|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|exceptions/count|Exceptions|Count|Count|Nombre combiné de toutes les exceptions non interceptées.|cloud/roleName,cloud/roleInstance,client/type|
|exceptions/browser|Exceptions du navigateur|Count|Count|Nombre d’exceptions non interceptées levées dans le navigateur.|client/isServer,cloud/roleName|
|exceptions/server|Exceptions de serveur|Count|Count|Nombre d’exceptions non interceptées levées dans l’application serveur.|client/isServer,cloud/roleName,cloud/roleInstance|
|traces/count|Traces|Count|Count|Nombre de documents de traces|trace/severityLevel,operation/synthetic,cloud/roleName,cloud/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ServiceApiHit|Correspondances totales de l'API de service|Count|Count|Nombre total de correspondances de l'API de service|ActivityType,ActivityName|
|ServiceApiLatency|Latence globale de l'API de service|Millisecondes|Average|Latence globale des demandes de l'API de service|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|ServiceApiResult|Résultats totaux de l'API de service|Count|Count|Nombre total de résultats de l'API de service|ActivityType,ActivityName,StatusCode,StatusCodeClass|
|SaturationShoebox|Saturation globale du coffre|Pourcentage|Average|Capacité de coffre utilisée|ActivityType,ActivityName,TransactionType|
|Disponibilité|Disponibilité globale du coffre|Pourcentage|Average|Disponibilité des demandes de coffre|ActivityType,ActivityName,StatusCode,StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CacheUtilization|Utilisation du cache|Pourcentage|Average|Niveau d’utilisation dans l’étendue du cluster|None|
|QueryDuration|Durée de la requête|Millisecondes|Average|Durée des requêtes en secondes|QueryStatus|
|IngestionUtilization|Utilisation de l’ingestion|Pourcentage|Average|Ratio d’emplacements d’ingestion utilisés dans le cluster|None|
|KeepAlive|Keep alive|Count|Average|Contrôle d’intégrité indiquant que le cluster répond aux requêtes|None|
|IngestionVolumeInMB|Volume d’ingestion (en Mo)|Count|Total|Volume total de données ingérées dans le cluster (en Mo)|Base de données|
|IngestionLatencyInSeconds|Latence d’ingestion (en secondes)|Secondes|Average|Durée d’ingestion entre la source (par ex., message dans Event Hub) et le cluster en secondes|None|
|EventsProcessedForEventHubs|Événements traités (pour Event/IoT Hubs)|Count|Total|Nombre d’événements traités par le cluster lors de l’ingestion à partir d’Event/IoT Hub|EventStatus|
|IngestionResult|Résultat de l’ingestion|Count|Count|Nombre d’opérations d’ingestion|IngestionResultDetails|
|UC|UC|Pourcentage|Average|Niveau d’utilisation de l’UC|None|
|ContinuousExportNumOfRecordsExported|Exportation continue : nombre d’enregistrements exportés|Count|Total|Nombre d’enregistrements exportés, déclenchés pour chaque artefact de stockage écrit pendant l’opération d’exportation|None|
|ExportUtilization|Utilisation de l’exportation|Pourcentage|Maximale|Utilisation de l’exportation|None|
|ContinuousExportPendingCount|Nombre en attente d’exportations continues|Count|Maximale|Nombre de travaux d’exportation continue en attente prêts pour l’exécution|None|
|ContinuousExportMaxLatenessMinutes|Minutes de retard max. pour l’exportation continue|Count|Maximale|Retard maximal en minutes de toutes les exportations continues en attente et prêtes pour l’exécution|None|
|ContinuousExportResult|Résultat de l’exportation continue|Count|Count|Indique si l’exportation continue a réussi ou échoué|ContinuousExportName,Result,Database|
|StreamingIngestDuration|Durée de l’ingestion en streaming|Millisecondes|Average|Durée de l’ingestion en streaming en millisecondes|None|
|StreamingIngestDataRate|Taux de données d’ingestion en streaming|Count|Average|Taux de données d’ingestion en streaming (Mo par seconde)|None|
|SteamingIngestRequestRate|Taux de demandes d’ingestion en streaming|Count|RateRequestsPerSecond|Taux de demandes d’ingestion en streaming (Mo par seconde)|None|
|StreamingIngestResults|Résultat de l’ingestion en streaming|Count|Average|Résultat de l’ingestion en streaming|Résultats|
|TotalNumberOfConcurrentQueries|Nombre total de demandes simultanées|Count|Total|Nombre total de demandes simultanées|None|
|TotalNumberOfThrottledQueries|Nombre total de demandes limitées|Count|Total|Nombre total de demandes limitées|None|
|TotalNumberOfThrottledCommands|Nombre total de commandes limitées|Count|Total|Nombre total de commandes limitées|CommandType|
|TotalNumberOfExtents|Nombre total d’étendues|Count|Total|Nombre total d’étendues de données|None|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|Exécutions démarrées|Count|Total|Nombre d’exécutions de flux de travail démarrées.|None|
|RunsCompleted|Exécutions terminées|Count|Total|Nombre d’exécutions de flux de travail terminées.|None|
|RunsSucceeded|Exécutions réussies|Count|Total|Nombre d’exécutions de flux de travail ayant réussi.|None|
|RunsFailed|Exécutions ayant échoué|Count|Total|Nombre d’exécutions de flux de travail ayant échoué.|None|
|RunsCancelled|Exécutions annulées|Count|Total|Nombre d’exécutions de flux de travail annulées.|None|
|RunLatency|Latence d’exécution|Secondes|Average|Latence des exécutions de flux de travail terminées.|None|
|RunSuccessLatency|Latence d’exécution ayant réussi|Secondes|Average|Latence des exécutions de flux de travail ayant réussi.|None|
|RunThrottledEvents|Exécuter événements limités|Count|Total|Nombre d’actions de flux de travail ou d’événements limités par déclencheur.|None|
|RunStartThrottledEvents|Événements limités démarrés par l’exécution|Count|Total|Nombre d’événements limités de démarrage de l’exécution de flux de travail.|None|
|RunFailurePercentage|Pourcentage d’échec des exécutions|Pourcentage|Total|Pourcentage d’exécutions de flux de travail ayant échoué.|None|
|ActionsStarted|Actions démarrées |Count|Total|Nombre d’actions de flux de travail démarrées.|None|
|ActionsCompleted|Actions terminées |Count|Total|Nombre d’actions de flux de travail terminées.|None|
|ActionsSucceeded|Actions ayant réussi |Count|Total|Nombre d’actions de flux de travail ayant réussi.|None|
|ActionsFailed|Actions ayant échoué |Count|Total|Nombre d’actions de flux de travail ayant échoué.|None|
|ActionsSkipped|Actions ignorées |Count|Total|Nombre d’actions de flux de travail ignorées.|None|
|ActionLatency|Latence de l’action |Secondes|Average|Latence des actions de flux de travail terminés.|None|
|ActionSuccessLatency|Latence de réussite d’action |Secondes|Average|Latence des actions de flux de travail ayant réussi.|None|
|ActionThrottledEvents|Événements d’action limitée|Count|Total|Nombre d’événements limités d’action de flux de travail.|None|
|TriggersStarted|Déclenchements lancés |Count|Total|Nombre de déclencheurs de flux de travail démarrés.|None|
|TriggersCompleted|Déclenchements terminés |Count|Total|Nombre de déclencheurs de flux de travail terminés.|None|
|TriggersSucceeded|Déclenchements ayant réussi |Count|Total|Nombre de déclencheurs de flux de travail ayant réussi.|None|
|TriggersFailed|Déclenchements ayant échoué |Count|Total|Nombre de déclencheurs de flux de travail ayant échoué.|None|
|TriggersSkipped|Déclenchements ignorés|Count|Total|Nombre de déclencheurs de flux de travail ignorés.|None|
|TriggersFired|Déclenchements activés |Count|Total|Nombre de déclencheurs de flux de travail activés.|None|
|TriggerLatency|Latence de déclencheur |Secondes|Average|Latence des déclenchements de flux de travail terminés.|None|
|TriggerFireLatency|Latence d’activation de déclencheur |Secondes|Average|Latence des déclencheurs de flux de travail activés.|None|
|TriggerSuccessLatency|Latence déclencheur ayant réussi |Secondes|Average|Latence des déclencheurs de flux de travail ayant réussi.|None|
|TriggerThrottledEvents|Événements limités par déclencheur|Count|Total|Nombre d’événements de flux de travail limités par déclencheur.|None|
|BillableActionExecutions|Exécutions d’actions facturables|Count|Total|Nombre d’exécutions d’actions de flux de travail facturées.|None|
|BillableTriggerExecutions|Exécutions de déclencheurs facturables|Count|Total|Nombre d’exécutions de déclencheurs de flux de travail facturées.|None|
|TotalBillableExecutions|Nombre total d’exécutions facturables|Count|Total|Nombre d’exécutions de flux de travail facturées.|None|
|BillingUsageNativeOperation|Utilisation de la facturation pour les exécutions d’opérations natives|Count|Total|Nombre d’exécutions d’opérations natives facturées.|None|
|BillingUsageStandardConnector|Utilisation de la facturation pour les exécutions de connecteurs Standard|Count|Total|Nombre d’exécutions de connecteurs Standard facturées.|None|
|BillingUsageStorageConsumption|Utilisation de la facturation pour les exécutions de consommation du stockage|Count|Total|Nombre d’exécutions de consommation du stockage facturées.|None|
|BillingUsageNativeOperation|Utilisation de la facturation pour les exécutions d’opérations natives|Count|Total|Nombre d’exécutions d’opérations natives facturées.|None|
|BillingUsageStandardConnector|Utilisation de la facturation pour les exécutions de connecteurs Standard|Count|Total|Nombre d’exécutions de connecteurs Standard facturées.|None|
|BillingUsageStorageConsumption|Utilisation de la facturation pour les exécutions de consommation du stockage|Count|Total|Nombre d’exécutions de consommation du stockage facturées.|None|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|Exécutions démarrées|Count|Total|Nombre d’exécutions de flux de travail démarrées.|None|
|RunsCompleted|Exécutions terminées|Count|Total|Nombre d’exécutions de flux de travail terminées.|None|
|RunsSucceeded|Exécutions réussies|Count|Total|Nombre d’exécutions de flux de travail ayant réussi.|None|
|RunsFailed|Exécutions ayant échoué|Count|Total|Nombre d’exécutions de flux de travail ayant échoué.|None|
|RunsCancelled|Exécutions annulées|Count|Total|Nombre d’exécutions de flux de travail annulées.|None|
|RunLatency|Latence d’exécution|Secondes|Average|Latence des exécutions de flux de travail terminées.|None|
|RunSuccessLatency|Latence d’exécution ayant réussi|Secondes|Average|Latence des exécutions de flux de travail ayant réussi.|None|
|RunThrottledEvents|Exécuter événements limités|Count|Total|Nombre d’actions de flux de travail ou d’événements limités par déclencheur.|None|
|RunStartThrottledEvents|Événements limités démarrés par l’exécution|Count|Total|Nombre d’événements limités de démarrage de l’exécution de flux de travail.|None|
|RunFailurePercentage|Pourcentage d’échec des exécutions|Pourcentage|Total|Pourcentage d’exécutions de flux de travail ayant échoué.|None|
|ActionsStarted|Actions démarrées |Count|Total|Nombre d’actions de flux de travail démarrées.|None|
|ActionsCompleted|Actions terminées |Count|Total|Nombre d’actions de flux de travail terminées.|None|
|ActionsSucceeded|Actions ayant réussi |Count|Total|Nombre d’actions de flux de travail ayant réussi.|None|
|ActionsFailed|Actions ayant échoué |Count|Total|Nombre d’actions de flux de travail ayant échoué.|None|
|ActionsSkipped|Actions ignorées |Count|Total|Nombre d’actions de flux de travail ignorées.|None|
|ActionLatency|Latence de l’action |Secondes|Average|Latence des actions de flux de travail terminés.|None|
|ActionSuccessLatency|Latence de réussite d’action |Secondes|Average|Latence des actions de flux de travail ayant réussi.|None|
|ActionThrottledEvents|Événements d’action limitée|Count|Total|Nombre d’événements limités d’action de flux de travail.|None|
|TriggersStarted|Déclenchements lancés |Count|Total|Nombre de déclencheurs de flux de travail démarrés.|None|
|TriggersCompleted|Déclenchements terminés |Count|Total|Nombre de déclencheurs de flux de travail terminés.|None|
|TriggersSucceeded|Déclenchements ayant réussi |Count|Total|Nombre de déclencheurs de flux de travail ayant réussi.|None|
|TriggersFailed|Déclenchements ayant échoué |Count|Total|Nombre de déclencheurs de flux de travail ayant échoué.|None|
|TriggersSkipped|Déclenchements ignorés|Count|Total|Nombre de déclencheurs de flux de travail ignorés.|None|
|TriggersFired|Déclenchements activés |Count|Total|Nombre de déclencheurs de flux de travail activés.|None|
|TriggerLatency|Latence de déclencheur |Secondes|Average|Latence des déclenchements de flux de travail terminés.|None|
|TriggerFireLatency|Latence d’activation de déclencheur |Secondes|Average|Latence des déclencheurs de flux de travail activés.|None|
|TriggerSuccessLatency|Latence déclencheur ayant réussi |Secondes|Average|Latence des déclencheurs de flux de travail ayant réussi.|None|
|TriggerThrottledEvents|Événements limités par déclencheur|Count|Total|Nombre d’événements de flux de travail limités par déclencheur.|None|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Utilisation du processeur de flux de travail pour l’environnement de service d’intégration|Pourcentage|Average|Utilisation du processeur de flux de travail pour l’environnement de service d’intégration.|None|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Utilisation de la mémoire de flux de travail pour l’environnement de service d’intégration|Pourcentage|Average|Utilisation de la mémoire de flux de travail pour l’environnement de service d’intégration.|None|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Utilisation du processeur du connecteur pour l’environnement de service d’intégration|Pourcentage|Average|Utilisation du processeur du connecteur pour l’environnement de service d’intégration.|None|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Utilisation de la mémoire du connecteur pour l’environnement de service d’intégration|Pourcentage|Average|Utilisation de la mémoire du connecteur pour l’environnement de service d’intégration.|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Exécutions terminées|Exécutions terminées|Count|Total|Nombre d’exécutions réussies pour cet espace de travail|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Exécutions démarrées|Exécutions démarrées|Count|Total|Nombre d’exécutions démarrées pour cet espace de travail|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Exécutions échouées|Exécutions échouées|Count|Total|Nombre d’exécutions en échec pour cet espace de travail|Scenario,RunType,PublishedPipelineId,ComputeType,PipelineStepType|
|Enregistrements de modèles réussis|Enregistrements de modèles réussis|Count|Total|Nombre d’enregistrements de modèles ayant réussi dans cet espace de travail|Scénario|
|Enregistrements de modèles ayant échoué|Enregistrements de modèles ayant échoué|Count|Total|Nombre d’enregistrements de modèles ayant échoué dans cet espace de travail|Scenario,StatusCode|
|Déploiements de modèles commencés|Déploiements de modèles commencés|Count|Total|Nombre de déploiements de modèles ayant commencé dans cet espace de travail|Scénario|
|Déploiements de modèles réussis|Déploiements de modèles réussis|Count|Total|Nombre de déploiements de modèles ayant réussi dans cet espace de travail|Scénario|
|Échec du déploiement de modèle|Échec du déploiement de modèle|Count|Total|Nombre de déploiements de modèles ayant échoué dans cet espace de travail|Scenario,StatusCode|
|Nombre total de nœuds|Nombre total de nœuds|Count|Average|Nombre total de nœuds. Ce total comprend certains des nœuds actifs, des nœuds inactifs, des nœuds inutilisables, des nœuds reportés et des nœuds sortants|Scenario,ClusterName|
|Nœuds actifs|Nœuds actifs|Count|Average|Nombre de nœuds actifs. Ce sont les nœuds qui exécutent activement un travail.|Scenario,ClusterName|
|Nœuds inactifs|Nœuds inactifs|Count|Average|Nombre de nœuds inactifs. Les nœuds inactifs sont les nœuds qui n’exécutent aucun travail, mais qui peuvent accepter un nouveau travail, le cas échéant.|Scenario,ClusterName|
|Nœuds inutilisables|Nœuds inutilisables|Count|Average|Nombre de nœuds inutilisables. Les nœuds inutilisables ne sont pas fonctionnels en raison d’un problème insoluble. Azure recycle ces nœuds.|Scenario,ClusterName|
|Nœuds reportés|Nœuds reportés|Count|Average|Nombre de nœuds reportés. Ces nœuds sont les nœuds de basse priorité qui sont éloignés du pool de nœuds disponibles.|Scenario,ClusterName|
|Nœuds sortants|Nœuds sortants|Count|Average|Nombre de nœuds sortants. Les nœuds sortants sont les nœuds qui viennent de terminer le traitement d’un travail et qui passent à l’état inactif.|Scenario,ClusterName|
|Nombre total de cœurs|Nombre total de cœurs|Count|Average|Nombre total de cœurs|Scenario,ClusterName|
|Cœurs actifs|Cœurs actifs|Count|Average|Nombre de cœurs actifs|Scenario,ClusterName|
|Cœurs inactifs|Cœurs inactifs|Count|Average|Nombre de cœurs inactifs|Scenario,ClusterName|
|Cœurs inutilisables|Cœurs inutilisables|Count|Average|Nombre de cœurs inutilisables|Scenario,ClusterName|
|Cœurs réquisitionnés|Cœurs réquisitionnés|Count|Average|Nombre de cœurs réquisitionnés|Scenario,ClusterName|
|Cœurs sortants|Cœurs sortants|Count|Average|Nombre de cœurs sortants|Scenario,ClusterName|
|Pourcentage d’utilisation du quota|Pourcentage d’utilisation du quota|Count|Average|Pourcentage de quota utilisé|Scenario,ClusterName,VmFamilyName,VmPriority|
|CpuUtilization|CpuUtilization|Count|Average|UC (Préversion)|Scenario,runId,NodeId,CreatedTime|
|GpuUtilization|GpuUtilization|Count|Average|GPU (préversion)|Scenario,runId,NodeId,CreatedTime,DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Usage|Usage|Count|Count|Nombre d’appels d’API|ApiCategory,ApiName,ResultType,ResponseCode|
|Disponibilité|Disponibilité|Pourcentage|Average|Disponibilité des API|ApiCategory,ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets.|OutputFormat|
|SuccessE2ELatency|Latence de réussite de bout en bout|Millisecondes|Average|Latence moyenne des demandes réussies, en millisecondes.|OutputFormat|
|Demandes|Demandes|Count|Total|Demandes à un point de terminaison de streaming.|OutputFormat,HttpStatusCode,ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|AssetQuota|Quota de ressources|Count|Average|Nombre de ressources autorisées pour le compte de service multimédia actuel|None|
|AssetCount|Nombre de ressources|Count|Average|Nombre de ressources déjà créées dans le compte de service multimédia actuel|None|
|AssetQuotaUsedPercentage|Pourcentage du quota de ressources utilisé|Pourcentage|Average|Pourcentage de ressources utilisées dans le compte de service multimédia actuel|None|
|ContentKeyPolicyQuota|Quota de stratégies de clé de contenu|Count|Average|Nombre de stratégies de clé de contenu autorisées pour le compte de service multimédia actuel|None|
|ContentKeyPolicyCount|Nombre de stratégies de clé de contenu|Count|Average|Nombre de stratégies de clé de contenu déjà créées dans le compte de service multimédia actuel|None|
|ContentKeyPolicyQuotaUsedPercentage|Pourcentage du quota de stratégies de clé de contenu utilisé|Pourcentage|Average|Pourcentage de stratégies de clé de contenu utilisées dans le compte de service multimédia actuel|None|
|StreamingPolicyQuota|Quota de stratégies de diffusion en continu|Count|Average|Nombre de stratégies de streaming autorisées pour le compte de service multimédia actuel|None|
|StreamingPolicyCount|Nombre de stratégies de diffusion en continu|Count|Average|Nombre de stratégies de streaming déjà créées dans le compte de service multimédia actuel|None|
|StreamingPolicyQuotaUsedPercentage|Pourcentage du quota de stratégies de diffusion en continu utilisé|Pourcentage|Average|Pourcentage de stratégies de streaming utilisées dans le compte de service multimédia actuel|None|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|AverageReadLatency|Latence de lecture moyenne|Millisecondes|Average|Latence de lecture moyenne en millisecondes par opération|None|
|AverageWriteLatency|Latence d’écriture moyenne|Millisecondes|Average|Latence d’écriture moyenne en millisecondes par opération|None|
|VolumeLogicalSize|Taille logique du volume|Octets|Average|Taille logique du volume (octets utilisés)|None|
|VolumeSnapshotSize|Taille d’instantané du volume|Octets|Average|Taille de tous les instantanés dans le volume|None|
|ReadIops|E/S par seconde en lecture|CountPerSecond|Average|Opérations d’E/S en lecture par seconde|None|
|WriteIops|E/S par seconde en écriture|CountPerSecond|Average|Opérations d’E/S en écriture par seconde|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Taille allouée utilisée du pool de volumes|Octets|Average|Taille allouée utilisée du pool|None|
|VolumePoolTotalLogicalSize|Taille logique totale du pool de volumes|Octets|Average|Somme de la taille logique de tous les volumes appartenant au pool|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BytesSentRate|Octets envoyés|Octets|Total|Nombre d’octets envoyés par l’interface réseau|None|
|BytesReceivedRate|Octets reçus|Octets|Total|Nombre d’octets reçus par l’interface réseau|None|
|PacketsSentRate|Paquets envoyés|Count|Total|Nombre de paquets envoyés par l’interface réseau|None|
|PacketsReceivedRate|Paquets reçus|Count|Total|Nombre de paquets reçus par l’interface réseau|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|VipAvailability|Disponibilité du chemin d’accès aux données|Count|Average|Disponibilité du chemin d’accès aux données d’équilibreur de charge moyenne par durée|FrontendIPAddress,FrontendPort|
|DipAvailability|État de la sonde d’intégrité|Count|Average|État de la sonde d’intégrité d’équilibreur de charge moyen par durée|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|ByteCount|Nombre d’octets|Count|Total|Nombre total d’octets transmis dans une période de temps|FrontendIPAddress,FrontendPort,Direction|
|PacketCount|Nombre de paquets|Count|Total|Nombre total de paquets transmis dans une période de temps|FrontendIPAddress,FrontendPort,Direction|
|SYNCount|Nombre de SYN|Count|Total|Nombre total de paquets SYN transmis dans une période de temps|FrontendIPAddress,FrontendPort,Direction|
|SnatConnectionCount|Nombre de connexions SNAT|Count|Total|Nombre total de connexions SNAT créées dans une période de temps|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|Ports SNAT alloués (Préversion)|Count|Total|Nombre total de ports SNAT alloués pendant la période|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|
|UsedSnatPorts|Ports SNAT utilisés (Préversion)|Count|Total|Nombre total de ports SNAT utilisés pendant une période|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QueryVolume|Volume de requête|Count|Total|Nombre de requêtes servies pour une zone DNS|None|
|RecordSetCount|Nombre de jeux d’enregistrements|Count|Maximale|Nombre de jeux d’enregistrements dans une zone DNS|None|
|RecordSetCapacityUtilization|Utilisation de la capacité du jeu d’enregistrements|Pourcentage|Maximale|Pourcentage de la capacité du jeu d’enregistrements utilisée par une zone DNS|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PacketsInDDoS|Paquets entrants DDoS|CountPerSecond|Maximale|Paquets entrants DDoS|None|
|PacketsDroppedDDoS|Paquets entrants ignorés DDoS|CountPerSecond|Maximale|Paquets entrants ignorés DDoS|None|
|PacketsForwardedDDoS|Paquets entrants transférés DDoS|CountPerSecond|Maximale|Paquets entrants transférés DDoS|None|
|TCPPacketsInDDoS|Paquets TCP entrants DDoS|CountPerSecond|Maximale|Paquets TCP entrants DDoS|None|
|TCPPacketsDroppedDDoS|Paquets TCP entrants TCP ignorés DDoS|CountPerSecond|Maximale|Paquets TCP entrants TCP ignorés DDoS|None|
|TCPPacketsForwardedDDoS|Paquets TCP entrants transférés DDoS|CountPerSecond|Maximale|Paquets TCP entrants transférés DDoS|None|
|UDPPacketsInDDoS|Paquets UDP entrants DDoS|CountPerSecond|Maximale|Paquets UDP entrants DDoS|None|
|UDPPacketsDroppedDDoS|Paquets UDP entrants ignorés DDoS|CountPerSecond|Maximale|Paquets UDP entrants ignorés DDoS|None|
|UDPPacketsForwardedDDoS|Paquets UDP entrants transférés DDoS|CountPerSecond|Maximale|Paquets UDP entrants transférés DDoS|None|
|BytesInDDoS|Octets entrants DDoS|BytesPerSecond|Maximale|Octets entrants DDoS|None|
|BytesDroppedDDoS|Octets entrants supprimés DDoS|BytesPerSecond|Maximale|Octets entrants supprimés DDoS|None|
|BytesForwardedDDoS|Octets entrants transférés DDoS|BytesPerSecond|Maximale|Octets entrants transférés DDoS|None|
|TCPBytesInDDoS|Octets TCP entrants DDoS|BytesPerSecond|Maximale|Octets TCP entrants DDoS|None|
|TCPBytesDroppedDDoS|Octets TCP entrants supprimés DDoS|BytesPerSecond|Maximale|Octets TCP entrants supprimés DDoS|None|
|TCPBytesForwardedDDoS|Octets TCP entrants transférés DDoS|BytesPerSecond|Maximale|Octets TCP entrants transférés DDoS|None|
|UDPBytesInDDoS|Octets UDP entrants DDoS|BytesPerSecond|Maximale|Octets UDP entrants DDoS|None|
|UDPBytesDroppedDDoS|Octets UDP entrants supprimés DDoS|BytesPerSecond|Maximale|Octets UDP entrants supprimés DDoS|None|
|UDPBytesForwardedDDoS|Octets UDP entrants transférés DDoS|BytesPerSecond|Maximale|Octets UDP entrants transférés DDoS|None|
|IfUnderDDoSAttack|Sous attaque DDoS ou non|Count|Maximale|Sous attaque DDoS ou non|None|
|DDoSTriggerTCPPackets|Paquets TCP entrants pour déclencher l’atténuation DDoS|CountPerSecond|Maximale|Paquets TCP entrants pour déclencher l’atténuation DDoS|None|
|DDoSTriggerUDPPackets|Paquets UDP entrants pour déclencher l’atténuation DDoS|CountPerSecond|Maximale|Paquets UDP entrants pour déclencher l’atténuation DDoS|None|
|DDoSTriggerSYNPackets|Paquets SYN entrants pour déclencher l’atténuation DDoS|CountPerSecond|Maximale|Paquets SYN entrants pour déclencher l’atténuation DDoS|None|
|VipAvailability|Disponibilité du chemin d’accès aux données|Count|Average|Disponibilité d’adresse IP moyenne par durée|Port|
|ByteCount|Nombre d’octets|Count|Total|Nombre total d’octets transmis dans une période de temps|Port,Direction|
|PacketCount|Nombre de paquets|Count|Total|Nombre total de paquets transmis dans une période de temps|Port,Direction|
|SynCount|Nombre de SYN|Count|Total|Nombre total de paquets SYN transmis dans une période de temps|Port,Direction|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Durée aller-retour pour les commandes ping à destination d’une machine virtuelle|Millisecondes|Average|Durée aller-retour pour les commandes ping envoyées à une machine virtuelle de destination|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Commandes ping à destination d’une machine virtuelle qui ont échoué|Pourcentage|Average|Pourcentage du nombre d’échecs de commandes ping par rapport au total de commandes ping envoyées à une machine virtuelle de destination|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ApplicationRuleHit|Nombre d'accès aux règles d'application|Count|Total|Nombre d'accès aux règles d'application|Status,Reason,Protocol|
|NetworkRuleHit|Nombre d’accès aux règles de réseau|Count|Total|Nombre d’accès aux règles de réseau|Status,Reason,Protocol|
|FirewallHealth|État d’intégrité du pare-feu|Pourcentage|Average|État d’intégrité du pare-feu|Status,Reason|
|DataProcessed|Données traitées|Octets|Total|Quantité totale de données traitées par le pare-feu|None|
|SNATPortUtilization|Utilisation des ports SNAT|Pourcentage|Average|Utilisation des ports SNAT|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Débit|Débit|BytesPerSecond|Average|Nombre d’octets par seconde servis par Application Gateway|None|
|UnhealthyHostCount|Nombre d’hôtes non intègres|Count|Average|Nombre d’hôtes principaux non intègres|BackendSettingsPool|
|HealthyHostCount|Nombre d’hôtes intègres|Count|Average|Nombre d’hôtes principaux intègres|BackendSettingsPool|
|TotalRequests|Total de requêtes|Count|Total|Nombre de requêtes réussies servies par Application Gateway|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Demandes par minute par hôte sain|Count|Average|Nombre moyen de demandes par minute par hôte back-end sain dans un pool|BackendSettingsPool|
|FailedRequests|Demandes ayant échoué|Count|Total|Nombre de requêtes en échec servies par Application Gateway|BackendSettingsPool|
|ResponseStatus|État de la réponse|Count|Total|État de la réponse HTTP retourné par Application Gateway|HttpStatusGroup|
|CurrentConnections|Connexions courantes|Count|Total|Nombre de connexions courantes établies avec Application Gateway|None|
|CpuUtilization|Utilisation du processeur|Pourcentage|Average|Utilisation actuelle du processeur d’Application Gateway|None|
|CapacityUnits|Unités de capacité actuelles|Count|Average|Unités de capacité consommées|None|
|ComputeUnits|Unités Compute actuelles|Count|Average|Unités Compute consommées|None|
|BackendResponseStatus|État de la réponse du back-end|Count|Total|Nombre de codes de réponse HTTP générés par les membres du back-end. Cela n’inclut pas les codes de réponse générés par Application Gateway.|BackendServer,BackendPool,BackendHttpSetting,HttpStatusGroup|
|TlsProtocol|Protocole TLS du client|Count|Total|Nombre de demandes TLS et non-TLS lancées par le client qui a établi la connexion avec Application Gateway. Pour afficher la distribution du protocole TLS, filtrez par la dimension Protocole TLS.|Listener,TlsProtocol|
|BytesSent|Octets envoyés|Octets|Total|Nombre total d’octets envoyés par Application Gateway aux clients|Écouteur|
|BytesReceived|Octets reçus|Octets|Total|Nombre total d’octets reçus par Application Gateway à partir des clients|Écouteur|
|ClientRtt|RTT client|Millisecondes|Average|Durée moyenne des allers-retours entre les clients et Application Gateway. Cette métrique indique le temps nécessaire à l’établissement des connexions et au retour des accusés de réception|Écouteur|
|ApplicationGatewayTotalTime|Durée totale d’Application Gateway|Millisecondes|Average|Temps moyen nécessaire pour le traitement d’une requête et l’envoi de la réponse. Elle est calculée en fonction de l’intervalle moyen entre le moment où Application Gateway reçoit le premier octet d’une requête HTTP et le moment où l’opération d’envoi d’une réponse se termine. Il est important de noter que cela implique généralement le temps de traitement d’Application Gateway, le temps pendant lequel les paquets de requête et de réponse transitent sur le réseau et le temps que le serveur principal a mis pour répondre.|Écouteur|
|BackendConnectTime|Temps de connexion au back-end|Millisecondes|Average|Temps passé à établir une connexion avec un back-end|Listener,BackendServer,BackendPool,BackendHttpSetting|
|BackendFirstByteResponseTime|Temps de réponse du premier octet du back-end|Millisecondes|Average|Intervalle de temps entre le début de l’établissement d’une connexion au principal et la réception du premier octet de l’en-tête de réponse, ce qui correspond approximativement au temps de traitement du principal|Listener,BackendServer,BackendPool,BackendHttpSetting|
|BackendLastByteResponseTime|Temps de réponse du dernier octet du back-end|Millisecondes|Average|Intervalle de temps entre le début de l’établissement d’une connexion au principal et la réception du dernier octet du corps de la réponse|Listener,BackendServer,BackendPool,BackendHttpSetting|
|MatchedCount|Distribution totale des règles du pare-feu d’applications web|Count|Total|Distribution totale des règles du pare-feu d’applications web pour le trafic entrant|RuleGroup,RuleId|
|BlockedCount|Distribution des règles de demandes bloquées du pare-feu d’applications web|Count|Total|Distribution des règles de demandes bloquées du pare-feu d’applications web|RuleGroup,RuleId|
|BlockedReqCount|Nombre de demandes bloquées du pare-feu d’applications web|Count|Total|Nombre de demandes bloquées du pare-feu d’applications web|None|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|AverageBandwidth|Bande passante S2S de passerelle|BytesPerSecond|Average|Bande passante site à site moyenne d’une passerelle en octets par seconde|None|
|P2SBandwidth|Bande passante P2S de passerelle|BytesPerSecond|Average|Bande passante point à site moyenne d’une passerelle en octets par seconde|None|
|P2SConnectionCount|Nombre de connexions P2S|Count|Maximale|Nombre de connexions point à site d’une passerelle|Protocol|
|TunnelAverageBandwidth|Bande passante de tunnel|BytesPerSecond|Average|Bande passante moyenne d’un tunnel en octets par seconde|ConnectionName,RemoteIP|
|TunnelEgressBytes|Octets de sortie de tunnel|Octets|Total|Octets sortants d’un tunnel|ConnectionName,RemoteIP|
|TunnelIngressBytes|Octets d’entrée de tunnel|Octets|Total|Octets entrants d’un tunnel|ConnectionName,RemoteIP|
|TunnelEgressPackets|Paquets de sortie de tunnel|Count|Total|Nombre de paquets sortants d’un tunnel|ConnectionName,RemoteIP|
|TunnelIngressPackets|Paquets en entrée de tunnel|Count|Total|Nombre de paquets entrants d’un tunnel|ConnectionName,RemoteIP|
|TunnelEgressPacketDropTSMismatch|Rejet de paquets TS de sortie de tunnel pour incompatibilité|Count|Total|Nombre de rejets de paquets sortants du sélecteur de trafic pour incompatibilité dans un tunnel|ConnectionName,RemoteIP|
|TunnelIngressPacketDropTSMismatch|Rejet de paquets TS d’entrée de tunnel pour incompatibilité|Count|Total|Nombre de rejets de paquets entrants du sélecteur de trafic pour incompatibilité dans un tunnel|ConnectionName,RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Count|Average|Niveau d’éclairage de réception en dBm|Link,Lane|
|TxLightLevel|TxLightLevel|Count|Average|Niveau d’éclairage de transmission en dBm|Link,Lane|
|AdminState|AdminState|Count|Average|État administrateur du port|Lien|
|LineProtocol|LineProtocol|Count|Average|État du protocole de ligne du port|Lien|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits entrant dans Azure par seconde|Lien|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits sortant d’Azure par seconde|Lien|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits entrant dans Azure par seconde|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits sortant d’Azure par seconde|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Average|Bits entrant dans Azure par seconde|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Average|Bits sortant d’Azure par seconde|PeeredCircuitSKey|
|BgpAvailability|Disponibilité du protocole BGP|Pourcentage|Average|Disponibilité du protocole BGP entre MSEE et tous les pairs.|PeeringType,Peer|
|ArpAvailability|Disponibilité du protocole ARP|Pourcentage|Average|Disponibilité du protocole ARP entre MSEE et tous les pairs.|PeeringType,Peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Average|Octets de données d’entrée abandonnés par seconde|None|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Average|Octets de données de sortie abandonnés par seconde|None|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits entrant dans Azure par seconde|None|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits sortant d’Azure par seconde|None|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits entrant dans Azure par seconde|None|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits sortant d’Azure par seconde|None|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits entrant dans Azure par seconde|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits sortant d’Azure par seconde|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QpsByEndpoint|Requêtes par point de terminaison renvoyé|Count|Total|Nombre de fois où un point de terminaison Traffic Manager a été renvoyé dans le laps de temps donné|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|État du point de terminaison par point de terminaison|Count|Maximale|1 si l’état de sondage d’un point de terminaison est « activé », 0 dans le cas contraire.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ProbesFailedPercent|% de sondes ayant échoué|Pourcentage|Average|% de sondes de surveillance de connectivité ayant échoué|None|
|AverageRoundtripMs|Avg. Durée aller-retour (ms)|Millisecondes|Average|Durée aller-retour réseau moyenne (ms) pour les sondes de surveillance de connectivité envoyées entre la source et la destination|None|
|ChecksFailedPercent|Pourcentage de vérifications ayant échoué (préversion)|Pourcentage|Average|% de vérifications de supervision de connectivité ayant échoué|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|
|RoundTripTimeMs|Durée aller-retour (ms) (préversion)|Millisecondes|Average|Durée aller-retour en millisecondes pour les vérifications de supervision de la connectivité|SourceAddress,SourceName,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|RequestCount|Nombre de requêtes|Count|Total|Nombre de requêtes clients prises en charge par le proxy HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|RequestSize|Taille de la requête|Octets|Total|Nombre d’octets envoyés en tant que requêtes de clients au proxy HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|ResponseSize|Taille de la réponse|Octets|Total|Nombre d’octets envoyés en tant que réponses du proxy HTTP/S aux clients|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BillableResponseSize|Taille de la réponse facturable|Octets|Total|Nombre d’octets facturables (2 Ko minimum par demande) envoyés en tant que réponses du proxy HTTP/S aux clients.|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendRequestCount|Nombre de requêtes de backend|Count|Total|Nombre de requêtes envoyées du proxy HTTP/S aux serveurs principaux|HttpStatus,HttpStatusGroup,Backend|
|BackendRequestLatency|Latence de requête du backend|Millisecondes|Average|Temps calculé à partir du moment où la requête est envoyée par le proxy HTTP/S au serveur principal jusqu’à ce que le proxy HTTP/S reçoive le dernier octet de la réponse du serveur principal|Backend|
|TotalLatency|Latence totale|Millisecondes|Average|Temps calculé à partir du moment où la requête du client est reçue par le proxy HTTP/S jusqu’à ce que le client accuse réception du dernier octet de la réponse du proxy HTTP/S|HttpStatus,HttpStatusGroup,ClientRegion,ClientCountry|
|BackendHealthPercentage|Pourcentage d’intégrité du backend|Pourcentage|Average|Pourcentage de sondes d’intégrité réussies du proxy HTTP/S vers les serveurs principaux|Backend,BackendPool|
|WebApplicationFirewallRequestCount|Nombre de requêtes du pare-feu d’applications web|Count|Total|Nombre de requêtes clients traitées par le pare-feu d’applications web|PolicyName,RuleName,Action|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|registration.all|Opérations d’inscription|Count|Total|Total des opérations d’inscription réussies (requêtes de mises à jour de créations et suppressions). |None|
|registration.create|Opérations de création d’inscription|Count|Total|Total des créations d’inscription réussies.|None|
|registration.update|Opérations de mise à jour d’inscription|Count|Total|Total des mises à jour d’inscription réussies.|None|
|registration.get|Opérations de lecture d’inscription|Count|Total|Total des requêtes d’inscription réussies.|None|
|registration.delete|Opérations de suppression d’inscription|Count|Total|Total des suppressions d’inscription réussies.|None|
|incoming|Messages entrants|Count|Total|Total des appels d’API d’envoi réussis. |None|
|incoming.scheduled|Notifications Push planifiées envoyées|Count|Total|Notifications Push planifiées annulées|None|
|incoming.scheduled.cancel|Notifications Push planifiées annulées|Count|Total|Notifications Push planifiées annulées|None|
|scheduled.pending|Notifications planifiées en attente|Count|Total|Notifications planifiées en attente|None|
|installation.all|Opérations de gestion de l’installation|Count|Total|Opérations de gestion de l’installation|None|
|installation.get|Opérations d’obtention d’installation|Count|Total|Opérations d’obtention d’installation|None|
|installation.upsert|Opérations de création ou de mise à jour d’installation|Count|Total|Opérations de création ou de mise à jour d’installation|None|
|installation.patch|Opérations d’installation de correctif|Count|Total|Opérations d’installation de correctif|None|
|installation.delete|Opérations de suppression d’installation|Count|Total|Opérations de suppression d’installation|None|
|outgoing.allpns.success|Notifications réussies|Count|Total|Total des notifications réussies.|None|
|outgoing.allpns.invalidpayload|Erreurs de charge utile|Count|Total|Nombre d’opérations Push en échec, car le PNS a retourné une erreur de charge utile incorrecte.|None|
|outgoing.allpns.pnserror|Erreurs système de notification externe|Count|Total|Nombre d’opérations Push en échec en raison d’un problème de communication avec le PNS (à l’exclusion des problèmes d’authentification).|None|
|outgoing.allpns.channelerror|Erreurs de canal|Count|Total|Nombre d’opérations Push en échec, car le canal n’était pas valide ni associé à l’application appropriée, limité ou arrivé à expiration.|None|
|outgoing.allpns.badorexpiredchannel|Erreurs de canal incorrect ou arrivé à expiration|Count|Total|Nombre d’opérations Push en échec, car le canal/jeton/registrationId indiqué dans l’inscription est arrivé à expiration ou non valide.|None|
|outgoing.wns.success|Notifications WNS réussies|Count|Total|Total des notifications réussies.|None|
|outgoing.wns.invalidcredentials|Erreurs d’autorisation WNS (informations d’identification non valides)|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées. (Windows Live ne reconnaît pas les informations d’identification).|None|
|outgoing.wns.badchannel|Erreur de canal WNS incorrect|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément ChannelURI indiqué dans l'inscription n'a pas été reconnu (état WNS : 404 - Introuvable).|None|
|outgoing.wns.expiredchannel|Erreur de canal WNS arrivé à expiration|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément ChannelURI est arrivé à expiration (état WNS : 410 Supprimé).|None|
|outgoing.wns.throttled|Notifications WNS limitées|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car WNS limite cette application (état WNS : 406 Non acceptable).|None|
|outgoing.wns.tokenproviderunreachable|Erreurs d’autorisation WNS (inaccessible)|Count|Total|Windows Live n’est pas accessible.|None|
|outgoing.wns.invalidtoken|Erreurs d’autorisation WNS (jeton non valide)|Count|Total|Le jeton fourni à WNS n'est pas valide (état WNS : 401 - Non autorisé).|None|
|outgoing.wns.wrongtoken|Erreurs d’autorisation WNS (jeton incorrect)|Count|Total|Le jeton fourni à WNS est valide mais il concerne une autre application (état WNS : 403 - Interdit). Cela peut se produire si l’élément ChannelURI indiqué dans l’inscription est associé à une autre application. Vérifiez que l’application cliente est associée à l’application dont les informations d’identification figurent dans le hub de notification.|None|
|outgoing.wns.invalidnotificationformat|Format de notification WNS non valide|Count|Total|Le format de la notification n'est pas valide (état WNS : 400). Notez que WNS ne rejette pas toutes les charges utiles non valides.|None|
|outgoing.wns.invalidnotificationsize|Erreur de taille de notification WNS non valide|Count|Total|La charge utile de notification est trop grande (état WNS : 413).|None|
|outgoing.wns.channelthrottled|Canal WNS limité|Count|Total|La notification a été supprimée car l'élément ChannelURI contenu dans l'inscription est limité (en-tête de réponse WNS : X-WNS-NotificationStatus:channelThrottled).|None|
|outgoing.wns.channeldisconnected|Canal WNS déconnecté|Count|Total|La notification a été supprimée car l'élément ChannelURI contenu dans l'inscription est limité (en-tête de réponse WNS : X-WNS-DeviceConnectionStatus: déconnecté).|None|
|outgoing.wns.dropped|Notifications WNS supprimées|Count|Total|La notification a été supprimée, car l’élément ChannelURI indiqué dans l’inscription est limité (X-WNS-NotificationStatus : supprimé, mais pas X-WNS-DeviceConnectionStatus : déconnecté).|None|
|outgoing.wns.pnserror|Erreurs WNS|Count|Total|La notification n’a pas été remise en raison d’erreurs de communication avec WNS.|None|
|outgoing.wns.authenticationerror|Erreurs d’authentification WNS|Count|Total|La notification n’a pas été remise en raison d’erreurs de communication avec Windows Live, d’informations d’identification non valides ou d’un jeton incorrect.|None|
|outgoing.apns.success|Notifications APNS réussies|Count|Total|Total des notifications réussies.|None|
|outgoing.apns.invalidcredentials|Erreurs d’autorisation APNS|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|None|
|outgoing.apns.badchannel|Erreur de canal APNS incorrect|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car le jeton n'est pas valide (code d'état APNS : 8).|None|
|outgoing.apns.expiredchannel|Erreur de canal APNS arrivé à expiration|Count|Total|Nombre de jetons qui ont été invalidés par le canal de commentaires APNS.|None|
|outgoing.apns.invalidnotificationsize|Erreur de taille de notification APNS non valide|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car la charge utile était trop importante (code d'état APNS : 7).|None|
|outgoing.apns.pnserror|Erreurs APNS|Count|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec APNS.|None|
|outgoing.gcm.success|Notifications GCM réussies|Count|Total|Total des notifications réussies.|None|
|outgoing.gcm.invalidcredentials|Erreurs d’autorisation GCM (informations d’identification non valides)|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|None|
|outgoing.gcm.badchannel|Erreur de canal GCM incorrect|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément registrationId contenu dans l'inscription n'a pas été reconnu (résultat GCM : inscription non valide).|None|
|outgoing.gcm.expiredchannel|Erreur de canal GCM arrivé à expiration|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément registrationId contenu dans l'inscription n'a pas été reconnu (résultat GCM : NotRegistered).|None|
|outgoing.gcm.throttled|Notifications GCM limitées|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car GCM a limité cette application (code d'état GCM : 501-599 ou résultat : non disponible).|None|
|outgoing.gcm.invalidnotificationformat|Format de notification GCM non valide|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car la charge utile n'était pas correctement mise en forme (résultat GCM : InvalidDataKey ou InvalidTtl).|None|
|outgoing.gcm.invalidnotificationsize|Erreur de taille de notification GCM non valide|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car la charge utile était trop importante (résultat GCM : MessageTooBig).|None|
|outgoing.gcm.wrongchannel|Erreur de canal GCM incorrect|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément registrationId contenu dans l'inscription n'est pas associé à l'application actuelle (résultat GCM : InvalidPackageName).|None|
|outgoing.gcm.pnserror|Erreurs GCM|Count|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec GCM.|None|
|outgoing.gcm.authenticationerror|Erreurs d’authentification GCM|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car le PNS n'a pas accepté les informations d'identification fournies, celles-ci sont bloquées ou l'élément SenderId n'est pas correctement configuré dans l'application (résultat GCM : MismatchedSenderId).|None|
|outgoing.mpns.success|Notifications MPNS réussies|Count|Total|Total des notifications réussies.|None|
|outgoing.mpns.invalidcredentials|Informations d’identification MPNS non valides|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|None|
|outgoing.mpns.badchannel|Erreur de canal incorrect MPNS|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément ChannelURI contenu dans l'inscription n'a pas été reconnu (état MPNS : 404 - Introuvable).|None|
|outgoing.mpns.throttled|Notifications MPNS limitées|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car MPNS limite cette application (WNS MPNS : 406 Non acceptable).|None|
|outgoing.mpns.invalidnotificationformat|Format de notification MPNS non valide|Count|Total|Nombre d’opérations Push en échec, car la charge utile de la notification était trop importante.|None|
|outgoing.mpns.channeldisconnected|Canal MPNS déconnecté|Count|Total|Nombre d'opérations d'envoi (push) qui ont échoué car l'élément ChannelURI contenu dans l'inscription était déconnecté (état MPNS : 412 introuvable).|None|
|outgoing.mpns.dropped|Notifications MPNS supprimées|Count|Total|Nombre d'opérations d'envoi (push) qui ont été abandonnées par MPNS (en-tête de réponse MPNS : X-NotificationStatus: QueueFull ou Suppressed).|None|
|outgoing.mpns.pnserror|Erreurs MPNS|Count|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec MPNS.|None|
|outgoing.mpns.authenticationerror|Erreurs d’authentification MPNS|Count|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|None|
|notificationhub.pushes|Toutes les notifications sortantes|Count|Total|Toutes les notifications sortantes du hub de notification|None|
|incoming.all.requests|Toutes les demandes entrantes|Count|Total|Nombre total des demandes entrantes pour un hub de notification|None|
|incoming.all.failedrequests|Toutes les requêtes entrantes ayant échoué|Count|Total|Nombre total des demandes entrantes ayant échoué pour un hub de notification|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Inodes libres Average_%|% Free Inodes|Count|Average|Inodes libres Average_%|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Free Space|% Free Space|Count|Average|Average_% Free Space|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Inodes utilisés Average_%|% Used Inodes|Count|Average|Inodes utilisés Average_%|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Inodes utilisés Average_%|% Used Space|Count|Average|Inodes utilisés Average_%|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Octets de lecture/s Average_Disk|Nb d’octets de lecture de disque/s|Count|Average|Octets de lecture/s Average_Disk|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Reads/sec|Nb d’opérations de lectures de disque/s|Count|Average|Average_Disk Reads/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Count|Average|Average_Disk Transfers/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Octets d’écriture/s Average_Disk|Nb d’octets d’écriture de disque/s|Count|Average|Octets d’écriture/s Average_Disk|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Writes/sec|Nb d’opération d’écriture de disque/s|Count|Average|Average_Disk Writes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Mégaoctets Average_Free|Free Megabytes|Count|Average|Mégaoctets Average_Free|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Count|Average|Average_Logical Disk Bytes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Available Memory|% Available Memory|Count|Average|Average_% Available Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Count|Average|Average_% Available Swap Space|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Used Memory|% Used Memory|Count|Average|Average_% Used Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Count|Average|Average_% Used Swap Space|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Count|Average|Average_Available MBytes Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Count|Average|Average_Available MBytes Swap|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Count|Average|Average_Page Reads/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Count|Average|Average_Page Writes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pages/sec|Pages/sec|Count|Average|Average_Pages/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Count|Average|Average_Used MBytes Swap Space|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Count|Average|Average_Used Memory MBytes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Count|Average|Average_Total Bytes Transmitted|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Count|Average|Average_Total Bytes Received|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Bytes|Total Bytes|Count|Average|Average_Total Bytes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Count|Average|Average_Total Packets Transmitted|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Bytes Received|Total Packets Received|Count|Average|Average_Total Bytes Received|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Count|Average|Average_Total Rx Errors|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Rx Errors|Total Tx Errors|Count|Average|Average_Total Rx Errors|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Total Collisions|Total Collisions|Count|Average|Average_Total Collisions|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Count|Average|Average_Avg. Disk sec/Read|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|Count|Average|Average_Avg. Disk sec/Transfer|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Count|Average|Average_Avg. Disk sec/Write|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Count|Average|Average_Physical Disk Bytes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Count|Average|Average_Pct Privileged Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Pct User Time|Pct User Time|Count|Average|Average_Pct User Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Used Memory MBytes|Used Memory kBytes|Count|Average|Average_Used Memory MBytes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Count|Average|Average_Virtual Shared Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% DPC Time|% DPC Time|Count|Average|Average_% DPC Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Idle Time|% Idle Time|Count|Average|Average_% Idle Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Count|Average|Average_% Interrupt Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Count|Average|Average_% IO Wait Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Nice Time|% Nice Time|Count|Average|Average_% Nice Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Privileged Time|% Privileged Time|Count|Average|Average_% Privileged Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Temps processeur Average_%|% temps processeur|Count|Average|Temps processeur Average_%|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% User Time|% User Time|Count|Average|Average_% User Time|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Count|Average|Average_Free Physical Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Count|Average|Average_Free Space in Paging Files|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Count|Average|Average_Free Virtual Memory|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Processes|Processus|Count|Average|Average_Processes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Count|Average|Average_Size Stored In Paging Files|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Uptime|Uptime|Count|Average|Average_Uptime|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Users|Utilisateurs|Count|Average|Average_Users|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Count|Average|Average_Avg. Disk sec/Read|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Count|Average|Average_Avg. Disk sec/Write|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Longueur de file d’attente du disque Average_Current|Longueur actuelle de la file d'attente du disque|Count|Average|Longueur de file d’attente du disque Average_Current|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Reads/sec|Nb d’opérations de lectures de disque/s|Count|Average|Average_Disk Reads/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Count|Average|Average_Disk Transfers/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Disk Writes/sec|Nb d’opération d’écriture de disque/s|Count|Average|Average_Disk Writes/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Mégaoctets Average_Free|Free Megabytes|Count|Average|Mégaoctets Average_Free|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_% Free Space|% Free Space|Count|Average|Average_% Free Space|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Available MBytes|Nombre d’octets disponibles|Count|Average|Average_Available MBytes|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Octets validés en cours d’utilisation Average_%|% d’octets validés utilisés|Count|Average|Octets validés en cours d’utilisation Average_%|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes Received/sec|Octets reçus/s|Count|Average|Average_Bytes Received/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes Sent/sec|Octets envoyés/s|Count|Average|Average_Bytes Sent/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Average_Bytes Total/sec|Nombre total d’octets/s|Count|Average|Average_Bytes Total/sec|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Temps processeur Average_%|% temps processeur|Count|Average|Temps processeur Average_%|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Longueur de la file d’attente Average_Processor|Longueur de la file d’attente du processeur|Count|Average|Longueur de la file d’attente Average_Processor|Computer,ObjectName,InstanceName,CounterPath,SourceSystem|
|Heartbeat|Heartbeat|Count|Total|Heartbeat|Computer,OSType,Version,SourceComputerId|
|Update|Update|Count|Average|Update|Computer,Product,Classification,UpdateState,Optional,Approved|
|Événement|Événement|Count|Average|Événement|Source,EventLog,Computer,EventCategory,EventLevel,EventLevelName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|PrefixLatency|Latence du préfixe|Millisecondes|Average|Latence du préfixe médiane|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Disponibilité de la session V4|Pourcentage|Average|Disponibilité de la session V4|ConnectionId|
|SessionAvailabilityV6|Disponibilité de la session V6|Pourcentage|Average|Disponibilité de la session V6|ConnectionId|
|IngressTrafficRate|Débit du trafic d’entrée|BitsPerSecond|Average|Débit du trafic d’entrée en bits par seconde|ConnectionId|
|EgressTrafficRate|Débit du trafic de sortie|BitsPerSecond|Average|Débit du trafic de sortie en bits par seconde|ConnectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QueryDuration|Durée de la requête|Millisecondes|Average|Durée de la requête DAX dans le dernier intervalle|Aucune dimension|
|QueryPoolJobQueueLength|Threads : longueur de la file d'attente des travaux du pool de requêtes|Count|Average|Nombre de travaux contenus dans la file d’attente du pool de threads de requêtes.|Aucune dimension|
|qpu_high_utilization_metric|Utilisation élevée du QPU|Count|Total|Utilisation élevée du QPU pendant la dernière minute, 1 pour une utilisation élevée du QPU, sinon 0|Aucune dimension|
|memory_metric|Mémoire|Octets|Average|Mémoire. Plage de 0-3 Go pour A1, de 0-5 Go pour A2, de 0-10 Go pour A3, de 0-25 Go pour A4, de 0-50 Go pour A5 et de 0-100 Go pour A6|Aucune dimension|
|memory_thrashing_metric|Vidage de mémoire|Pourcentage|Average|Vidage de mémoire moyenne.|Aucune dimension|



## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Total|ListenerConnections ayant abouti pour Microsoft.Relay.|EntityName,OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Total|ClientError sur ListenerConnections pour Microsoft.Relay.|EntityName,OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Total|ServerError sur ListenerConnections pour Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-Success|SenderConnections-Success|Count|Total|SenderConnections ayant abouti pour Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Total|ClientError sur SenderConnections pour Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Total|ServerError sur SenderConnections pour Microsoft.Relay.|EntityName,OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Total|Nombre total de ListenerConnections pour Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Total|Nombre total de demandes SenderConnections pour Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Total|Nombre total d’ActiveConnections pour Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Total|Nombre total d’ActiveListeners pour Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Total|Nombre total de BytesTransferred pour Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Total|Nombre total de ListenerDisconnects pour Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Total|Nombre total de SenderDisconnects pour Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SearchLatency|Latence de recherche|Secondes|Average|Latence moyenne de recherche du service de recherche|None|
|SearchQueriesPerSecond|Requêtes de recherche par seconde|CountPerSecond|Average|Requêtes de recherche par seconde pour le service de recherche|None|
|ThrottledSearchQueriesPercentage|Pourcentage de requêtes de recherche limitées|Pourcentage|Average|Pourcentage de requêtes de recherche limitées par le service de recherche|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Requêtes ayant réussi|Count|Total|Nombre total de demandes ayant réussi pour un espace de noms|EntityName,OperationResult|
|ServerErrors|Erreurs de serveur.|Count|Total|Erreurs de serveur pour Microsoft.ServiceBus.|EntityName,OperationResult|
|UserErrors|Erreurs d’utilisateur.|Count|Total|Erreurs d’utilisateur pour Microsoft.ServiceBus.|EntityName,OperationResult|
|ThrottledRequests|Demandes limitées.|Count|Total|Demandes limitées pour Microsoft.ServiceBus.|EntityName,OperationResult|
|IncomingRequests|Demandes entrantes|Count|Total|Demandes entrantes pour Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Messages entrants|Count|Total|Messages entrants pour Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Messages sortants|Count|Total|Messages sortants pour Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Count|Total|Nombre total de connexions actives pour Microsoft.ServiceBus.|None|
|ConnectionsOpened|Connexions ouvertes.|Count|Average|Connexions ouvertes pour Microsoft.ServiceBus.|EntityName|
|ConnectionsClosed|Connexions fermées.|Count|Average|Connexions fermées pour Microsoft.ServiceBus.|EntityName|
|Taille|Taille|Octets|Average|Taille d’une file d’attente/rubrique en octets.|EntityName|
|Messages|Nombre de messages dans une file d’attente/rubrique.|Count|Average|Nombre de messages dans une file d’attente/rubrique.|EntityName|
|ActiveMessages|Nombre de messages actifs dans une file d’attente/rubrique.|Count|Average|Nombre de messages actifs dans une file d’attente/rubrique.|EntityName|
|DeadletteredMessages|Nombre de messages de lettres mortes dans une file d’attente/rubrique.|Count|Average|Nombre de messages de lettres mortes dans une file d’attente/rubrique.|EntityName|
|ScheduledMessages|Nombre de messages planifiés dans une file d’attente/rubrique.|Count|Average|Nombre de messages planifiés dans une file d’attente/rubrique.|EntityName|
|NamespaceCpuUsage|UC|Pourcentage|Maximale|Métrique d’utilisation du processeur de l’espace de noms Service Bus Premium.|Réplica|
|NamespaceMemoryUsage|Utilisation de la mémoire|Pourcentage|Maximale|Métrique d’utilisation de la mémoire de l’espace de noms Service Bus Premium|Réplica|
|CPUXNS|Processeur (déprécié)|Pourcentage|Maximale|Métrique d’utilisation du processeur de l’espace de noms Service Bus Premium. Cette métrique est dépréciée. Utilisez la métrique de processeur (NamespaceCpuUsage) à la place.|Réplica|
|WSXNS|Utilisation de la mémoire (déprécié)|Pourcentage|Maximale|Métrique d’utilisation de la mémoire de l’espace de noms Service Bus Premium Cette métrique est déconseillée. Utilisez la métrique d’utilisation de la mémoire (NamespaceMemoryUsage) à la place.|Réplica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|Processeur alloué à ce conteneur en millicoeurs|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Octets|Average|Mémoire allouée à ce conteneur en Mo|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|Utilisation réelle du processeur en millicoeurs|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|ActualMemory|ActualMemory|Octets|Average|Utilisation réelle de la mémoire en octets|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|CpuUtilization|CpuUtilization|Pourcentage|Average|Utilisation du processeur pour ce conteneur en pourcentage de AllocatedCpu|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Pourcentage|Average|Utilisation du processeur pour ce conteneur en pourcentage de AllocatedCpu|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|État de l'application Service Fabric Mesh|ApplicationName,Status|
|ServiceStatus|ServiceStatus|Count|Average|État d’intégrité d’un service dans l'application Service Fabric Mesh|ApplicationName,Status,ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|État d’intégrité d’un réplica de service dans l'application Service Fabric Mesh|ApplicationName,Status,ServiceName,ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Average|État du conteneur dans l'application Service Fabric Mesh|ApplicationName,ServiceName,CodePackageName,ServiceReplicaName,Status|
|RestartCount|RestartCount|Count|Average|Nombre de redémarrage d'un conteneur dans l'application Service Fabric Mesh|ApplicationName,Status,ServiceName,ServiceReplicaName,CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ConnectionCount|Nombre de connexions|Count|Maximale|Nombre de connexions utilisateur|Point de terminaison|
|MessageCount|Nombre de messages|Count|Total|Nombre total de messages.|None|
|InboundTraffic|Trafic entrant|Octets|Total|Trafic entrant de service|None|
|OutboundTraffic|Trafic sortant|Octets|Total|Trafic sortant de service|None|
|UserErrors|Erreurs d’utilisateur|Pourcentage|Maximale|Pourcentage d’erreurs d’utilisateur|None|
|SystemErrors|Erreurs système|Pourcentage|Maximale|Pourcentage d’erreurs système|None|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage UC|Pourcentage|Average|Pourcentage UC|None|
|physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Average|Pourcentage E/S des données|None|
|log_write_percent|Pourcentage E/S du journal|Pourcentage|Average|Pourcentage E/S du journal. Non applicable aux entrepôts de données.|None|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Average|Pourcentage DTU. S’applique aux bases de données basées sur DTU.|None|
|storage|Espace de données utilisé|Octets|Maximale|Espace de données utilisé. Non applicable aux entrepôts de données.|None|
|connection_successful|Connexions réussies|Count|Total|Connexions réussies|None|
|connection_failed|Connexions ayant échoué|Count|Total|Connexions ayant échoué|None|
|blocked_by_firewall|Bloqué par le pare-feu|Count|Total|Bloqué par le pare-feu|None|
|deadlock|Blocages|Count|Total|Interblocages. Non applicable aux entrepôts de données.|None|
|storage_percent|Pourcentage d'espace de données utilisé|Pourcentage|Maximale|Pourcentage d’espace de données utilisé. Non applicable aux entrepôts de données ou base de données hyperscale.|None|
|xtp_storage_percent|Pourcentage de stockage OLTP en mémoire|Pourcentage|Average|Pourcentage de stockage OLTP en mémoire. Non applicable aux entrepôts de données.|None|
|workers_percent|Pourcentage de travaux|Pourcentage|Average|Pourcentage de Workers. Non applicable aux entrepôts de données.|None|
|sessions_percent|Pourcentage de sessions|Pourcentage|Average|Pourcentage de sessions. Non applicable aux entrepôts de données.|None|
|dtu_limit|Limite DTU|Count|Average|Limite DTU. S’applique aux bases de données basées sur DTU.|None|
|dtu_used|DTU utilisé|Count|Average|DTU utilisé. S’applique aux bases de données basées sur DTU.|None|
|cpu_limit|Limite UC|Count|Average|Limite UC. S’applique aux bases de données basées sur vCore.|None|
|cpu_used|UC utilisée|Count|Average|UC utilisée. S’applique aux bases de données basées sur vCore.|None|
|dwu_limit|Limite DWU|Count|Maximale|Limite DWU. S’applique uniquement aux entrepôts de données.|None|
|dwu_consumption_percent|Pourcentage DWU|Pourcentage|Maximale|Pourcentage DWU. S’applique uniquement aux entrepôts de données.|None|
|dwu_used|DWU utilisé|Count|Maximale|DWU utilisé. S’applique uniquement aux entrepôts de données.|None|
|cache_hit_percent|Pourcentage d’accès au cache|Pourcentage|Maximale|Pourcentage d’accès au cache. S’applique uniquement aux entrepôts de données.|None|
|cache_used_percent|Pourcentage de cache utilisé|Pourcentage|Maximale|Pourcentage de cache utilisé. S’applique uniquement aux entrepôts de données.|None|
|sqlserver_process_core_percent|Pourcentage de cœurs de processus SQL Server|Pourcentage|Maximale|Pourcentage d’utilisation de l’UC pour le processus SQL Server, tel qu’il est mesuré par le système d’exploitation. Actuellement disponible uniquement pour les bases de données serverless.|None|
|sqlserver_process_memory_percent|Pourcentage de mémoire de processus SQL Server|Pourcentage|Maximale|Pourcentage d’utilisation de la mémoire pour le processus SQL Server, tel qu’il est mesuré par le système d’exploitation. Actuellement disponible uniquement pour les bases de données serverless.|None|
|tempdb_data_size|Taille du fichier de données tempdb en kilo-octets|Count|Maximale|Taille du fichier de données tempdb en kilo-octets. Non applicable aux entrepôts de données.|None|
|tempdb_log_size|Taille du fichier journal de tempdb en kilo-octets|Count|Maximale|Taille du fichier journal de tempdb en kilo-octets. Non applicable aux entrepôts de données.|None|
|tempdb_log_used_percent|Pourcentage d’utilisation du journal tempdb|Pourcentage|Maximale|Pourcentage d’utilisation du journal tempdb. Non applicable aux entrepôts de données.|None|
|local_tempdb_usage_percent|Pourcentage de tempdb locale|Pourcentage|Average|Pourcentage de tempdb locale. S’applique uniquement aux entrepôts de données.|None|
|app_cpu_billed|Processeur d'application facturé|Count|Total|Processeur d’application facturé. S’applique aux bases de données serverless.|None|
|app_cpu_percent|Pourcentage processeur d'application|Pourcentage|Average|Pourcentage processeur d'application. S’applique aux bases de données serverless.|None|
|app_memory_percent|Pourcentage de mémoire de l’application|Pourcentage|Average|Pourcentage de mémoire de l’application. S’applique aux bases de données serverless.|None|
|allocated_data_storage|Espace de données alloué|Octets|Average|Stockage des données alloué. Non applicable aux entrepôts de données.|None|
|memory_usage_percent|Pourcentage de mémoire|Pourcentage|Maximale|Pourcentage de mémoire. S’applique uniquement aux entrepôts de données.|None|
|full_backup_size_bytes|Taille de stockage de sauvegarde complète|Octets|Maximale|Taille de stockage de sauvegarde complète cumulée. S’applique aux bases de données basées sur vCore. Ne s'applique pas aux bases de données Hyperscale.|None|
|diff_backup_size_bytes|Taille de stockage de sauvegarde différentielle|Octets|Maximale|Taille de stockage de sauvegarde différentielle cumulée. S’applique aux bases de données basées sur vCore. Ne s'applique pas aux bases de données Hyperscale.|None|
|log_backup_size_bytes|Taille de stockage de fichier journal|Octets|Maximale|Taille de stockage de fichier journal cumulée. S’applique aux bases de données basées sur vCore. Ne s'applique pas aux bases de données Hyperscale.|None|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Pourcentage UC|Pourcentage|Average|Pourcentage UC|None|
|database_cpu_percent|Pourcentage UC|Pourcentage|Average|Pourcentage UC|DatabaseResourceId|
|physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Average|Pourcentage E/S des données|None|
|database_physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Average|Pourcentage E/S des données|DatabaseResourceId|
|log_write_percent|Pourcentage E/S du journal|Pourcentage|Average|Pourcentage E/S du journal|None|
|database_log_write_percent|Pourcentage E/S du journal|Pourcentage|Average|Pourcentage E/S du journal|DatabaseResourceId|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Average|Pourcentage DTU. S’applique aux pools élastiques basés sur DTU.|None|
|database_dtu_consumption_percent|Pourcentage DTU|Pourcentage|Average|Pourcentage DTU|DatabaseResourceId|
|storage_percent|Pourcentage d'espace de données utilisé|Pourcentage|Average|Pourcentage d'espace de données utilisé|None|
|workers_percent|Pourcentage de travaux|Pourcentage|Average|Pourcentage de travaux|None|
|database_workers_percent|Pourcentage de travaux|Pourcentage|Average|Pourcentage de travaux|DatabaseResourceId|
|sessions_percent|Pourcentage de sessions|Pourcentage|Average|Pourcentage de sessions|None|
|database_sessions_percent|Pourcentage de sessions|Pourcentage|Average|Pourcentage de sessions|DatabaseResourceId|
|eDTU_limit|Limite eDTU|Count|Average|Limite eDTU. S’applique aux pools élastiques basés sur DTU.|None|
|storage_limit|Taille maximale des données|Octets|Average|Taille maximale des données|None|
|eDTU_used|eDTU utilisé|Count|Average|eDTU utilisé. S’applique aux pools élastiques basés sur DTU.|None|
|database_eDTU_used|eDTU utilisé|Count|Average|eDTU utilisé|DatabaseResourceId|
|storage_used|Espace de données utilisé|Octets|Average|Espace de données utilisé|None|
|database_storage_used|Espace de données utilisé|Octets|Average|Espace de données utilisé|DatabaseResourceId|
|xtp_storage_percent|Pourcentage de stockage OLTP en mémoire|Pourcentage|Average|Pourcentage de stockage OLTP en mémoire|None|
|cpu_limit|Limite UC|Count|Average|Limite UC. S’applique aux pools élastiques basés sur vCore.|None|
|database_cpu_limit|Limite UC|Count|Average|Limite UC|DatabaseResourceId|
|cpu_used|UC utilisée|Count|Average|UC utilisée. S’applique aux pools élastiques basés sur vCore.|None|
|database_cpu_used|UC utilisée|Count|Average|UC utilisée|DatabaseResourceId|
|sqlserver_process_core_percent|Pourcentage de cœurs de processus SQL Server|Pourcentage|Maximale|Utilisation du processeur en pourcentage du processus SQL DB. S’applique aux pools élastiques.|None|
|sqlserver_process_memory_percent|Pourcentage de mémoire de processus SQL Server|Pourcentage|Maximale|Utilisation de la mémoire en pourcentage du processus SQL DB. S’applique aux pools élastiques.|None|
|tempdb_data_size|Taille du fichier de données tempdb en kilo-octets|Count|Maximale|Taille du fichier de données tempdb en kilo-octets|None|
|tempdb_log_size|Taille du fichier journal de tempdb en kilo-octets|Count|Maximale|Taille du fichier journal de tempdb en kilo-octets|None|
|tempdb_log_used_percent|Pourcentage d’utilisation du journal tempdb|Pourcentage|Maximale|Pourcentage d’utilisation du journal tempdb|None|
|allocated_data_storage|Espace de données alloué|Octets|Average|Espace de données alloué|None|
|database_allocated_data_storage|Espace de données alloué|Octets|Average|Espace de données alloué|DatabaseResourceId|
|allocated_data_storage_percent|Pourcentage d'espace de données alloué|Pourcentage|Maximale|Pourcentage d'espace de données alloué|None|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Average|Pourcentage DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Pourcentage DTU|Pourcentage|Average|Pourcentage DTU|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Espace de données utilisé|Octets|Average|Espace de données utilisé|ElasticPoolResourceId|
|database_storage_used|Espace de données utilisé|Octets|Average|Espace de données utilisé|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|DTU utilisé|Count|Average|DTU utilisé|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|virtual_core_count|Nombre de cœurs virtuels|Count|Average|Nombre de cœurs virtuels|None|
|avg_cpu_percent|Pourcentage d’UC moyenne|Pourcentage|Average|Pourcentage d’UC moyenne|None|
|reserved_storage_mb|Espace de stockage réservé|Count|Average|Espace de stockage réservé|None|
|storage_space_used_mb|Espace de stockage utilisé|Count|Average|Espace de stockage utilisé|None|
|io_requests|Nombre de requêtes d’E/S|Count|Average|Nombre de requêtes d’E/S|None|
|io_bytes_read|Octets d’E/S lus|Octets|Average|Octets d’E/S lus|None|
|io_bytes_written|Octets d’E/S écrits|Octets|Average|Octets d’E/S écrits|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Capacité utilisée|Octets|Average|Capacité de compte utilisée|None|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType,GeoType,ApiName,Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType,ApiName,Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType,ApiName,Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Average|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType,ApiName,Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Average|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType,ApiName,Authentication|
|Disponibilité|Disponibilité|Pourcentage|Average|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType,ApiName,Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Capacité d’objet blob|Octets|Average|Quantité de stockage utilisée par le service BLOB du compte de stockage, en octets.|BlobType,Tier|
|BlobCount|Nombre d’objets blob|Count|Average|Nombre d’objets blob dans le service BLOB du compte de stockage.|BlobType,Tier|
|ContainerCount|Nombre de conteneurs d’objets blob|Count|Average|Nombre de conteneurs d’objets blob dans le service BLOB du compte de stockage.|None|
|IndexCapacity|Capacité d'index|Octets|Average|Stockage utilisé par l'index (hiérarchique) d'ADLS Gen2 en octets.|None|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType,GeoType,ApiName,Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType,ApiName,Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType,ApiName,Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Average|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType,ApiName,Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Average|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType,ApiName,Authentication|
|Disponibilité|Disponibilité|Pourcentage|Average|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType,ApiName,Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Capacité de la table|Octets|Average|Quantité de stockage utilisée par le service de Table du compte de stockage, en octets.|None|
|TableCount|Nombre de tables|Count|Average|Nombre de tables dans le service de Table du compte de stockage.|None|
|TableEntityCount|Nombre d’entités de table|Count|Average|Nombre d’entités de table dans le service de Table du compte de stockage.|None|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType,GeoType,ApiName,Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType,ApiName,Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType,ApiName,Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Average|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType,ApiName,Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Average|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType,ApiName,Authentication|
|Disponibilité|Disponibilité|Pourcentage|Average|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType,ApiName,Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|Capacité de fichiers|Octets|Average|Quantité de stockage utilisée par le service de Fichier du compte de stockage, en octets.|FileShare|
|FileCount|Nombre de fichiers|Count|Average|Nombre de fichiers dans le service de Fichier du compte de stockage.|FileShare|
|FileShareCount|Nombre de partages de fichiers|Count|Average|Nombre de partage de fichiers dans le service de Fichier du compte de stockage.|None|
|FileShareSnapshotCount|Nombre d’instantanés de partage de fichiers|Count|Average|Nombre d’instantanés présents sur le partage dans le service Files du compte de stockage.|FileShare|
|FileShareSnapshotSize|Taille d’instantané de partage de fichiers|Octets|Average|Quantité de stockage utilisée par les instantanés dans le service Fichier du compte de stockage, en octets.|FileShare|
|FileShareQuota|Taille du quota de partage de fichiers|Octets|Average|Limite supérieure de la quantité de stockage pouvant être utilisée par le service Azure Files, en octets.|FileShare|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType,GeoType,ApiName,Authentication,FileShare|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType,ApiName,Authentication,FileShare|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType,ApiName,Authentication,FileShare|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Average|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType,ApiName,Authentication,FileShare|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Average|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType,ApiName,Authentication,FileShare|
|Disponibilité|Disponibilité|Pourcentage|Average|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType,ApiName,Authentication,FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Capacité de la file d’attente|Octets|Average|Quantité de stockage utilisée par le service de File d’attente du compte de stockage, en octets.|None|
|QueueCount|Nombre de files d’attente|Count|Average|Nombre de files d’attente dans le service de File d’attente du compte de stockage.|None|
|QueueMessageCount|Nombre de messages dans la file d’attente|Count|Average|Nombre approximatif de messages en file d’attente dans le service de File d’attente du compte de stockage.|None|
|Transactions|Transactions|Count|Total|Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses.|ResponseType,GeoType,ApiName,Authentication|
|Entrée|Entrée|Octets|Total|Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure.|GeoType,ApiName,Authentication|
|Sortie|Sortie|Octets|Total|Quantité de données de sortie, en octets. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables.|GeoType,ApiName,Authentication|
|SuccessServerLatency|Latence du serveur avec requête réussie|Millisecondes|Average|Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency.|GeoType,ApiName,Authentication|
|SuccessE2ELatency|Latence E2E de réussite|Millisecondes|Average|Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse.|GeoType,ApiName,Authentication|
|Disponibilité|Disponibilité|Pourcentage|Average|Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée.|GeoType,ApiName,Authentication|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ClientIOPS|Total des IOPS du client|Count|Average|Taux des opérations de fichier client traitées par le cache.|None|
|ClientLatency|Latence moyenne du client|Millisecondes|Average|Latence moyenne des opérations de fichier client dans le cache de stockage.|None|
|ClientReadIOPS|IOPS de lecture du client|CountPerSecond|Average|Opérations de lecture du client par seconde.|None|
|ClientReadThroughput|Débit moyen de lecture du cache|BytesPerSecond|Average|Taux de transfert des données de lecture du client.|None|
|ClientWriteIOPS|IOPS d’écriture du client|CountPerSecond|Average|Opérations d’écriture du client par seconde.|None|
|ClientWriteThroughput|Débit moyen d’écriture dans le cache|BytesPerSecond|Average|Taux de transfert des données d’écriture du client.|None|
|ClientMetadataReadIOPS|IOPS de lecture de métadonnées client|CountPerSecond|Average|Taux des opérations de fichier client envoyées au cache, à l’exclusion des lectures de données qui ne modifient pas l’état persistant.|None|
|ClientMetadataWriteIOPS|IOPS d’écriture de métadonnées client|CountPerSecond|Average|Taux des opérations de fichier client envoyées au cache, à l’exclusion des écritures de données qui modifient l’état persistant.|None|
|ClientLockIOPS|IOPS de verrouillage du client|CountPerSecond|Average|Opérations de verrouillage de fichier client par seconde.|None|
|StorageTargetHealth|Intégrité de la cible de stockage|Count|Average|Résultats booléens du test de connectivité entre le cache et les cibles de stockage.|None|
|Uptime|Uptime|Count|Average|Résultats booléens du test de connectivité entre le cache et le système de supervision.|None|
|StorageTargetIOPS|Total des IOPS dans StorageTarget|Count|Average|Taux de toutes les opérations de fichier que le cache envoie à un StorageTarget particulier.|StorageTarget|
|StorageTargetWriteIOPS|IOPS d’écriture dans StorageTarget|Count|Average|Taux des opérations d’écriture de fichier que le cache envoie à un StorageTarget particulier.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Débit d’écriture asynchrone dans StorageTarget|BytesPerSecond|Average|Vitesse à laquelle le cache écrit les données dans un StorageTarget particulier de manière asynchrone. Il s’agit d’écritures opportunistes qui n’entraînent pas le blocage des clients.|StorageTarget|
|StorageTargetSyncWriteThroughput|Débit d’écriture synchrone dans StorageTarget|BytesPerSecond|Average|Vitesse à laquelle le cache écrit les données dans un StorageTarget particulier de manière synchrone. Il s’agit d’écritures qui provoquent le blocage des clients.|StorageTarget|
|StorageTargetTotalWriteThroughput|Débit total d’écriture dans StorageTarget|BytesPerSecond|Average|Vitesse totale à laquelle le cache écrit les données dans un StorageTarget particulier.|StorageTarget|
|StorageTargetLatency|Latence de StorageTarget|Millisecondes|Average|Latence moyenne de l’aller-retour de toutes les opérations de fichier que le cache envoie à un StorageTarget particulier.|StorageTarget|
|StorageTargetMetadataReadIOPS|IOPS de lecture des métadonnées dans StorageTarget|CountPerSecond|Average|Taux des opérations de fichier qui ne modifient pas l’état persistant, hormis l’opération de lecture, que le cache envoie à un StorageTarget particulier.|StorageTarget|
|StorageTargetMetadataWriteIOPS|IOPS d’écriture des métadonnées dans StorageTarget|CountPerSecond|Average|Taux des opérations de fichier qui modifient l’état persistant, hormis l’opération d’écriture, que le cache envoie à un StorageTarget particulier.|StorageTarget|
|StorageTargetReadIOPS|IOPS de lecture dans StorageTarget|CountPerSecond|Average|Taux des opérations de lecture de fichier que le cache envoie à un StorageTarget particulier.|StorageTarget|
|StorageTargetReadAheadThroughput|Débit de lecture anticipée dans StorageTarget|BytesPerSecond|Average|Vitesse à laquelle le cache lit de manière opportuniste les données dans StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|Débit de remplissage de StorageTarget|BytesPerSecond|Average|Vitesse à laquelle le cache lit les données dans StorageTarget pour gérer une opération non réussie dans le cache.|StorageTarget|
|StorageTargetTotalReadThroughput|Débit total de lecture dans StorageTarget|BytesPerSecond|Average|Vitesse totale à laquelle le cache lit les données dans un StorageTarget particulier.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Résultat de session de synchronisation|Count|Average|Métrique consignant une valeur de 1 chaque fois que le point de terminaison de serveur termine une session de synchronisation avec le point de terminaison cloud|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Fichiers synchronisés|Count|Total|Nombre de fichiers synchronisés|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Fichiers ne se synchronisant pas|Count|Total|Nombre de fichiers dont la synchronisation a échoué|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Octets synchronisés|Octets|Total|Taille totale des fichiers transférés pour les sessions de synchronisation|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|État du serveur en ligne|Count|Maximale|Métrique consignant une valeur de 1 chaque fois que le serveur inscrit enregistre une pulsation avec le point de terminaison cloud|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Rappel de hiérarchisation cloud|Octets|Total|Taille totale des données rappelées par le serveur|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Taille de rappel de la hiérarchisation cloud|Octets|Total|taille des données rappelées ;|SyncGroupName,ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Débit de rappel de la hiérarchisation cloud|BytesPerSecond|Average|Taille de débit de rappel des données|SyncGroupName,ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Taille de rappel de hiérarchisation cloud par application|Octets|Total|Taille des données rappelées par application|SyncGroupName,ServerName,ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Fichiers synchronisés|Count|Total|Nombre de fichiers synchronisés|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Fichiers ne se synchronisant pas|Count|Total|Nombre de fichiers dont la synchronisation a échoué|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Octets synchronisés|Octets|Total|Taille totale des fichiers transférés pour les sessions de synchronisation|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Fichiers synchronisés|Count|Total|Nombre de fichiers synchronisés|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Fichiers ne se synchronisant pas|Count|Total|Nombre de fichiers dont la synchronisation a échoué|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Octets synchronisés|Octets|Total|Taille totale des fichiers transférés pour les sessions de synchronisation|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ServerHeartbeat|État du serveur en ligne|Count|Maximale|Métrique consignant une valeur de 1 chaque fois que le serveur inscrit enregistre une pulsation avec le point de terminaison cloud|ServerResourceId,ServerName|
|ServerRecallIOTotalSizeBytes|Rappel de hiérarchisation cloud|Octets|Total|Taille totale des données rappelées par le serveur|ServerResourceId,ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|ResourceUtilization|Utilisation de % d’unités de diffusion|Pourcentage|Maximale|Utilisation de % d’unités de diffusion|LogicalName,PartitionId|
|InputEvents|Événements d’entrée|Count|Total|Événements d’entrée|LogicalName,PartitionId|
|InputEventBytes|Octets des événements d’entrée|Octets|Total|Octets des événements d’entrée|LogicalName,PartitionId|
|LateInputEvents|Événements d’entrée tardifs|Count|Total|Événements d’entrée tardifs|LogicalName,PartitionId|
|OutputEvents|Événements de sortie|Count|Total|Événements de sortie|LogicalName,PartitionId|
|ConversionErrors|Erreurs de conversion de données|Count|Total|Erreurs de conversion de données|LogicalName,PartitionId|
|Erreurs|Erreurs d’exécution|Count|Total|Erreurs d’exécution|LogicalName,PartitionId|
|DroppedOrAdjustedEvents|Événements en désordre|Count|Total|Événements en désordre|LogicalName,PartitionId|
|AMLCalloutRequests|Requêtes de fonction|Count|Total|Requêtes de fonction|LogicalName,PartitionId|
|AMLCalloutFailedRequests|Requêtes de fonction ayant échoué|Count|Total|Requêtes de fonction ayant échoué|LogicalName,PartitionId|
|AMLCalloutInputEvents|Événements de fonction|Count|Total|Événements de fonction|LogicalName,PartitionId|
|DeserializationError|Erreurs de désérialisation d’entrée|Count|Total|Erreurs de désérialisation d’entrée|LogicalName,PartitionId|
|EarlyInputEvents|Événements d’entrée précoces|Count|Total|Événements d’entrée précoces|LogicalName,PartitionId|
|OutputWatermarkDelaySeconds|Délai en filigrane|Secondes|Maximale|Délai en filigrane|LogicalName,PartitionId|
|InputEventsSourcesBacklogged|Événements d'entrée en backlog|Count|Maximale|Événements d'entrée en backlog|LogicalName,PartitionId|
|InputEventsSourcesPerSecond|Sources d'entrée reçues|Count|Total|Sources d'entrée reçues|LogicalName,PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Messages reçus en entrée|Count|Total|Nombre de messages lus à partir de la totalité des Event Hubs ou des sources d’événements IoT Hub|None|
|IngressReceivedInvalidMessages|Messages non valides reçus en entrée|Count|Total|Nombre de messages non valides lus à partir de la totalité des Event Hubs ou des sources d’événements IoT Hub|None|
|IngressReceivedBytes|Octets reçus en entrée|Octets|Total|Nombre d’octets lus à partir de toutes les sources d’événements|None|
|IngressStoredBytes|Octets stockés en entrée|Octets|Total|Taille totale des événements traités correctement et disponibles pour une requête|None|
|IngressStoredEvents|Événements stockés en entrée|Count|Total|Nombre d’événements aplatis traités correctement et disponibles pour une requête|None|
|IngressReceivedMessagesTimeLag|Retard des messages reçus en entrée|Secondes|Maximale|Différence entre l’heure à laquelle le message est placé en file d’attente dans la source d’événement et l’heure à laquelle il est traité en entrée|None|
|IngressReceivedMessagesCountLag|Décalage de nombre des messages reçus en entrée|Count|Average|Différence entre le numéro de séquence du dernier message placé en file d’attente dans la partition source de l’événement et le numéro de séquence des messages traités en entrée|None|
|WarmStorageMaxProperties|Propriétés max de stockage chaud|Count|Maximale|Nombre maximal de propriétés utilisées autorisées par l’environnement pour la référence SKU S1/S2 et nombre maximal de propriétés autorisées par le magasin Warm pour la référence SKU PAYG|None|
|WarmStorageUsedProperties|Propriétés de stockage chaud utilisées |Count|Maximale|Nombre de propriétés utilisées par l’environnement pour la référence SKU S1/S2 et nombre de propriétés utilisées par le magasin Warm pour la référence SKU PAYG|None|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Messages reçus en entrée|Count|Total|Nombre de messages lus à partir de la source de l’événement|None|
|IngressReceivedInvalidMessages|Messages non valides reçus en entrée|Count|Total|Nombre de messages non valides lus à partir de la source de l’événement|None|
|IngressReceivedBytes|Octets reçus en entrée|Octets|Total|Nombre d’octets lus à partir de la source de l’événement|None|
|IngressStoredBytes|Octets stockés en entrée|Octets|Total|Taille totale des événements traités correctement et disponibles pour une requête|None|
|IngressStoredEvents|Événements stockés en entrée|Count|Total|Nombre d’événements aplatis traités correctement et disponibles pour une requête|None|
|IngressReceivedMessagesTimeLag|Retard des messages reçus en entrée|Secondes|Maximale|Différence entre l’heure à laquelle le message est placé en file d’attente dans la source d’événement et l’heure à laquelle il est traité en entrée|None|
|IngressReceivedMessagesCountLag|Décalage de nombre des messages reçus en entrée|Count|Average|Différence entre le numéro de séquence du dernier message placé en file d’attente dans la partition source de l’événement et le numéro de séquence des messages traités en entrée|None|
|WarmStorageMaxProperties|Propriétés max de stockage chaud|Count|Maximale|Nombre maximal de propriétés utilisées autorisées par l’environnement pour la référence SKU S1/S2 et nombre maximal de propriétés autorisées par le magasin Warm pour la référence SKU PAYG|None|
|WarmStorageUsedProperties|Propriétés de stockage chaud utilisées |Count|Maximale|Nombre de propriétés utilisées par l’environnement pour la référence SKU S1/S2 et nombre de propriétés utilisées par le magasin Warm pour la référence SKU PAYG|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Average|Débit moyen du disque en raison des opérations de lecture pendant la période d’échantillonnage.|None|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Average|Débit moyen du disque en raison des opérations d'écriture pendant la période d’échantillonnage.|None|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Débit total du disque en raison des opérations de lecture au cours de la période d’échantillonnage.|None|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Débit total du disque en raison des opérations d'écriture au cours de la période d’échantillonnage.|None|
|DiskReadOperations|Opérations de lecture sur disque|Count|Total|Nombre d'opérations de lecture (E/S) au cours de la précédente période d’échantillonnage. Notez que ces opérations peuvent varier en taille.|None|
|DiskWriteOperations|Opérations d'écriture sur disque|Count|Total|Nombre d'opérations d'écriture (E/S) au cours de la précédente période d’échantillonnage. Notez que ces opérations peuvent varier en taille.|None|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|Nombre moyen d'opérations de lecture (E/S) au cours de la précédente période d’échantillonnage. Notez que ces opérations peuvent varier en taille.|None|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|Nombre moyen d'opérations d'écriture (E/S) au cours de la précédente période d’échantillonnage. Notez que ces opérations peuvent varier en taille.|None|
|DiskReadLatency|Latence de lecture sur disque|Millisecondes|Average|Latence de lecture totale. Somme des latences de lecture sur l'appareil et le noyau.|None|
|DiskWriteLatency|Latence d'écriture sur disque|Millisecondes|Average|Latence d'écriture totale. Somme des latences d'écriture sur l'appareil et le noyau.|None|
|NetworkInBytesPerSecond|Octets entrants réseau/s|BytesPerSecond|Average|Débit réseau moyen pour le trafic reçu.|None|
|NetworkOutBytesPerSecond|Octets sortants réseau/s|BytesPerSecond|Average|Débit réseau moyen pour le trafic transmis.|None|
|Network In|Network In|Octets|Total|Débit réseau total pour le trafic reçu.|None|
|Network Out|Network Out|Octets|Total|Débit réseau total pour le trafic transmis.|None|
|MemoryUsed|Mémoire utilisée|Octets|Average|Quantité de mémoire utilisée par la machine virtuelle.|None|
|MemoryGranted|Mémoire allouée|Octets|Average|Quantité de mémoire allouée par l'hôte à la machine virtuelle. La mémoire n’est pas allouée à l’hôte avant un premier accès et la mémoire allouée peut être échangée ou désallouée si le VMkernel a besoin de mémoire.|None|
|MemoryActive|Mémoire active|Octets|Average|Quantité de mémoire utilisée par la machine virtuelle dans la petite fenêtre de temps passée. Elle correspond à la quantité de mémoire « vraie » dont la machine virtuelle a actuellement besoin. La mémoire supplémentaire non utilisée peut être échangée ou désallouée sans incidence sur le niveau de performance de l'invité.|None|
|Percentage CPU|Percentage CPU|Pourcentage|Average|Utilisation du processeur. Cette valeur est signalée avec 100 % représentant tous les cœurs de processeur sur le système. Par exemple, une machine virtuelle bidirectionnelle utilisant 50 % d'un système à quatre cœurs utilise complètement deux cœurs.|None|
|PercentageCpuReady|Pourcentage de processeurs prêts|Millisecondes|Total|Correspond au temps passé à attendre que les processeurs soient disponibles dans l'intervalle de la dernière mise à jour.|None|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuPercentage|Pourcentage UC|Pourcentage|Average|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Average|Pourcentage de mémoire|Instance|
|DiskQueueLength|Longueur de file d'attente de disque|Count|Average|Longueur de file d'attente de disque|Instance|
|HttpQueueLength|Longueur de la file d’attente HTTP|Count|Average|Longueur de la file d’attente HTTP|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|TcpSynSent|Syn Sent - TCP|Count|Average|Syn Sent - TCP|Instance|
|TcpSynReceived|Syn Received - TCP|Count|Average|Syn Received - TCP|Instance|
|TcpEstablished|Established - TCP|Count|Average|Established - TCP|Instance|
|TcpFinWait1|Fin Wait 1 - TCP|Count|Average|Fin Wait 1 - TCP|Instance|
|TcpFinWait2|Fin Wait 2 - TCP|Count|Average|Fin Wait 2 - TCP|Instance|
|TcpClosing|Closing - TCP|Count|Average|Closing - TCP|Instance|
|TcpCloseWait|Close Wait - TCP|Count|Average|Close Wait - TCP|Instance|
|TcpLastAck|Last Ack - TCP|Count|Average|Last Ack - TCP|Instance|
|TcpTimeWait|Time Wait - TCP|Count|Average|Time Wait - TCP|Instance|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (à l’exclusion de Functions)

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|Instance|
|Demandes|Demandes|Count|Total|Demandes|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Count|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Count|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Count|Total|Http 3xx|Instance|
|Http401|Http 401|Count|Total|Http 401|Instance|
|Http403|Http 403|Count|Total|Http 403|Instance|
|Http404|Http 404|Count|Total|Http 404|Instance|
|Http406|Http 406|Count|Total|Http 406|Instance|
|Http4xx|Http 4xx|Count|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Count|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Average|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Average|Plage de travail moyenne de la mémoire|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Average|Temps de réponse moyen|Instance|
|AppConnections|Connexions|Count|Average|Connexions|Instance|
|Poignées|Nombre de descripteurs|Count|Average|Nombre de descripteurs|Instance|
|Threads|Nombre de threads|Count|Average|Nombre de threads|Instance|
|PrivateBytes|Octets privés|Octets|Average|Octets privés|Instance|
|IoReadBytesPerSecond|Octets lus par seconde (E/S)|BytesPerSecond|Total|Octets lus par seconde (E/S)|Instance|
|IoWriteBytesPerSecond|Octets écrits par seconde (E/S)|BytesPerSecond|Total|Octets écrits par seconde (E/S)|Instance|
|IoOtherBytesPerSecond|Autres octets par seconde (E/S)|BytesPerSecond|Total|Autres octets par seconde (E/S)|Instance|
|IoReadOperationsPerSecond|Opérations de lecture par seconde (E/S)|BytesPerSecond|Total|Opérations de lecture par seconde (E/S)|Instance|
|IoWriteOperationsPerSecond|Opérations d’écriture par seconde (E/S)|BytesPerSecond|Total|Opérations d’écriture par seconde (E/S)|Instance|
|IoOtherOperationsPerSecond|Autres opérations par seconde (E/S)|BytesPerSecond|Total|Autres opérations par seconde (E/S)|Instance|
|RequestsInApplicationQueue|Demandes dans la file d’attente d’application|Count|Average|Demandes dans la file d’attente d’application|Instance|
|CurrentAssemblies|Assemblys actuels|Count|Average|Assemblys actuels|Instance|
|TotalAppDomains|Total des domaines d’application|Count|Average|Total des domaines d’application|Instance|
|TotalAppDomainsUnloaded|Total des domaines d’application déchargés|Count|Average|Total des domaines d’application déchargés|Instance|
|Gen0Collections|Garbage collections de génération 0|Count|Total|Garbage collections de génération 0|Instance|
|Gen1Collections|Garbage collections de génération 1|Count|Total|Garbage collections de génération 1|Instance|
|Gen2Collections|Garbage collections de génération 2|Count|Total|Garbage collections de génération 2|Instance|
|HealthCheckStatus|État de contrôle d’intégrité|Count|Average|État de contrôle d’intégrité|Instance|
|FileSystemUsage|Utilisation de systèmes de fichiers|Octets|Average|Utilisation de systèmes de fichiers|None|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Functions)

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http5xx|Erreurs de serveur http|Count|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Average|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Average|Plage de travail moyenne de la mémoire|Instance|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Mo/millisecondes|Total|[Unités d’exécution de fonctions](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instance|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Count|Total|Nombre d’exécutions de fonctions|Instance|
|PrivateBytes|Octets privés|Octets|Average|Octets privés|Instance|
|IoReadBytesPerSecond|Octets lus par seconde (E/S)|BytesPerSecond|Total|Octets lus par seconde (E/S)|Instance|
|IoWriteBytesPerSecond|Octets écrits par seconde (E/S)|BytesPerSecond|Total|Octets écrits par seconde (E/S)|Instance|
|IoOtherBytesPerSecond|Autres octets par seconde (E/S)|BytesPerSecond|Total|Autres octets par seconde (E/S)|Instance|
|IoReadOperationsPerSecond|Opérations de lecture par seconde (E/S)|BytesPerSecond|Total|Opérations de lecture par seconde (E/S)|Instance|
|IoWriteOperationsPerSecond|Opérations d’écriture par seconde (E/S)|BytesPerSecond|Total|Opérations d’écriture par seconde (E/S)|Instance|
|IoOtherOperationsPerSecond|Autres opérations par seconde (E/S)|BytesPerSecond|Total|Autres opérations par seconde (E/S)|Instance|
|RequestsInApplicationQueue|Demandes dans la file d’attente d’application|Count|Average|Demandes dans la file d’attente d’application|Instance|
|CurrentAssemblies|Assemblys actuels|Count|Average|Assemblys actuels|Instance|
|TotalAppDomains|Total des domaines d’application|Count|Average|Total des domaines d’application|Instance|
|TotalAppDomainsUnloaded|Total des domaines d’application déchargés|Count|Average|Total des domaines d’application déchargés|Instance|
|Gen0Collections|Garbage collections de génération 0|Count|Total|Garbage collections de génération 0|Instance|
|Gen1Collections|Garbage collections de génération 1|Count|Total|Garbage collections de génération 1|Instance|
|Gen2Collections|Garbage collections de génération 2|Count|Total|Garbage collections de génération 2|Instance|
|HealthCheckStatus|État de contrôle d’intégrité|Count|Average|État de contrôle d’intégrité|Instance|
|FileSystemUsage|Utilisation de systèmes de fichiers|Octets|Average|Utilisation de systèmes de fichiers|None|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|Instance|
|Demandes|Demandes|Count|Total|Demandes|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Count|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Count|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Count|Total|Http 3xx|Instance|
|Http401|Http 401|Count|Total|Http 401|Instance|
|Http403|Http 403|Count|Total|Http 403|Instance|
|Http404|Http 404|Count|Total|Http 404|Instance|
|Http406|Http 406|Count|Total|Http 406|Instance|
|Http4xx|Http 4xx|Count|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Count|Total|Erreurs de serveur http|Instance|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Average|Plage de travail de la mémoire|Instance|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Average|Plage de travail moyenne de la mémoire|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Average|Temps de réponse moyen|Instance|
|HttpResponseTime|Temps de réponse|Secondes|Average|Temps de réponse|Instance|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Count|Total|Unités d’exécution de fonctions|Instance|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Count|Total|Nombre d’exécutions de fonctions|Instance|
|AppConnections|Connexions|Count|Average|Connexions|Instance|
|Poignées|Nombre de descripteurs|Count|Average|Nombre de descripteurs|Instance|
|Threads|Nombre de threads|Count|Average|Nombre de threads|Instance|
|PrivateBytes|Octets privés|Octets|Average|Octets privés|Instance|
|IoReadBytesPerSecond|Octets lus par seconde (E/S)|BytesPerSecond|Total|Octets lus par seconde (E/S)|Instance|
|IoWriteBytesPerSecond|Octets écrits par seconde (E/S)|BytesPerSecond|Total|Octets écrits par seconde (E/S)|Instance|
|IoOtherBytesPerSecond|Autres octets par seconde (E/S)|BytesPerSecond|Total|Autres octets par seconde (E/S)|Instance|
|IoReadOperationsPerSecond|Opérations de lecture par seconde (E/S)|BytesPerSecond|Total|Opérations de lecture par seconde (E/S)|Instance|
|IoWriteOperationsPerSecond|Opérations d’écriture par seconde (E/S)|BytesPerSecond|Total|Opérations d’écriture par seconde (E/S)|Instance|
|IoOtherOperationsPerSecond|Autres opérations par seconde (E/S)|BytesPerSecond|Total|Autres opérations par seconde (E/S)|Instance|
|RequestsInApplicationQueue|Demandes dans la file d’attente d’application|Count|Average|Demandes dans la file d’attente d’application|Instance|
|CurrentAssemblies|Assemblys actuels|Count|Average|Assemblys actuels|Instance|
|TotalAppDomains|Total des domaines d’application|Count|Average|Total des domaines d’application|Instance|
|TotalAppDomainsUnloaded|Total des domaines d’application déchargés|Count|Average|Total des domaines d’application déchargés|Instance|
|Gen0Collections|Garbage collections de génération 0|Count|Total|Garbage collections de génération 0|Instance|
|Gen1Collections|Garbage collections de génération 1|Count|Total|Garbage collections de génération 1|Instance|
|Gen2Collections|Garbage collections de génération 2|Count|Total|Garbage collections de génération 2|Instance|
|HealthCheckStatus|État de contrôle d’intégrité|Count|Average|État de contrôle d’intégrité|Instance|
|FileSystemUsage|Utilisation de systèmes de fichiers|Octets|Average|Utilisation de systèmes de fichiers|None|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|Demandes|Demandes|Count|Total|Demandes|Instance|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|Instance|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|Instance|
|Http101|HTTP 101|Count|Total|HTTP 101|Instance|
|Http2xx|Http 2xx|Count|Total|Http 2xx|Instance|
|Http3xx|Http 3xx|Count|Total|Http 3xx|Instance|
|Http401|Http 401|Count|Total|Http 401|Instance|
|Http403|Http 403|Count|Total|Http 403|Instance|
|Http404|Http 404|Count|Total|Http 404|Instance|
|Http406|Http 406|Count|Total|Http 406|Instance|
|Http4xx|Http 4xx|Count|Total|Http 4xx|Instance|
|Http5xx|Erreurs de serveur http|Count|Total|Erreurs de serveur http|Instance|
|AverageResponseTime|Temps de réponse moyen|Secondes|Average|Temps de réponse moyen|Instance|
|CpuPercentage|Pourcentage UC|Pourcentage|Average|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Average|Pourcentage de mémoire|Instance|
|DiskQueueLength|Longueur de file d'attente de disque|Count|Average|Longueur de file d'attente de disque|Instance|
|HttpQueueLength|Longueur de la file d’attente HTTP|Count|Average|Longueur de la file d’attente HTTP|Instance|
|ActiveRequests|Requêtes actives|Count|Total|Requêtes actives|Instance|
|TotalFrontEnds|Nombre total de serveurs frontaux|Count|Average|Nombre total de serveurs frontaux|None|
|SmallAppServicePlanInstances|Workers de plan App Service de petite taille|Count|Average|Workers de plan App Service de petite taille|None|
|MediumAppServicePlanInstances|Workers de plan App Service de taille moyenne|Count|Average|Workers de plan App Service de taille moyenne|None|
|LargeAppServicePlanInstances|Workers de plan App Service de grande taille|Count|Average|Workers de plan App Service de grande taille|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|Dimensions|
|---|---|---|---|---|---|
|WorkersTotal|Nombre total de workers|Count|Average|Nombre total de workers|None|
|WorkersAvailable|Workers disponibles|Count|Average|Workers disponibles|None|
|WorkersUsed|Workers utilisés|Count|Average|Workers utilisés|None|
|CpuPercentage|Pourcentage UC|Pourcentage|Average|Pourcentage UC|Instance|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Average|Pourcentage de mémoire|Instance|
## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les métriques dans Azure Monitor](data-platform.md)
* [Créer des alertes sur les métriques](alerts-overview.md)
* [Exporter des métriques vers le stockage, un hub d’événements ou Log Analytics](platform-logs-overview.md)
