---
title: Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques
description: Découvrez comment utiliser les métriques Azure Data Explorer pour superviser les performances, l’intégrité et l’utilisation du cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 82aa7f782dbb1842a29d55eef8983edd4afce8eb
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277415"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster et une ou plusieurs bases de données dans ce cluster. Ensuite, ingérez (chargez) des données dans une base de données pour pouvoir exécuter des requêtes dessus. Les métriques Azure Data Explorer fournissent des indicateurs clés concernant l’intégrité et les performances des ressources de cluster. Utilisez les métriques détaillées dans cet article pour superviser l’intégrité et les performances du cluster Azure Data Explorer dans votre scénario spécifique en tant que métriques autonomes. Vous pouvez également utiliser des métriques comme base pour les [tableaux de bord Azure](/azure/azure-portal/azure-portal-dashboards) et les [alertes Azure](/azure/azure-monitor/platform/alerts-metric-overview) operationnels.

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous n’avez pas encore d’abonnement Azure, [créez un compte Azure gratuit](https://azure.microsoft.com/free/)

* Créez [un cluster et une base de données](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="using-metrics"></a>Utilisation de métriques

Dans votre cluster Azure Data Explorer, sélectionnez **Métriques** pour ouvrir le volet de métriques et commencer l’analyse sur votre cluster.

![Sélection de l’option Métriques](media/using-metrics/select-metrics.png)

Dans le volet Métriques :

![Volet Métriques](media/using-metrics/metrics-pane.png)

1. Pour créer un graphique de métriques, sélectionnez le nom de la **Métrique** et l’**agrégation** pertinente par métrique. Les sélecteurs **Ressource** et **Espace de noms de métrique**  sont présélectionnés pour votre cluster Azure Data Explorer. Pour plus d’informations sur les différentes métriques, consultez [Métriques Azure Data Explorer prises en charge](#supported-azure-data-explorer-metrics).
1. Sélectionnez le bouton **Ajouter une métrique** pour afficher le tracé de plusieurs métriques sur le même graphique.
1. Sélectionnez le bouton **+ Nouveau graphique** pour afficher plusieurs graphiques dans une même vue.
1. Utilisez le sélecteur de temps pour modifier l’intervalle de temps (par défaut : les dernières 24 heures).
1. Utilisez [**Ajouter un filtre** et **Appliquer la division**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) pour les métriques qui ont des dimensions.
1. Sélectionnez **Épingler au tableau de bord** pour ajouter la configuration de votre graphique aux tableaux de bord afin de pouvoir le visualiser à nouveau.
1. Définissez **Nouvelle règle d’alerte** pour visualiser vos métriques à l’aide des critères définis. La nouvelle règle d’alerte inclura les dimensions de la ressource, de la métrique, du fractionnement et du filtre cibles de votre graphique. Modifiez ces paramètres dans le [volet de création de règle d’alerte](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Informations supplémentaires sur l’utilisation de [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Métriques Azure Data Explorer prises en charge

Les métriques Azure Data Explorer prises en charge sont classées en différentes catégories en fonction de leur utilisation. 

### <a name="cluster-health-metrics"></a>Métriques d’intégrité du cluster

Les métriques d’intégrité du cluster assurent le suivi de l’état général du cluster, notamment l’utilisation des ressources et de l’ingestion ainsi que la réactivité.

**Mesure** | **Unité** | **Agrégation** | **Description de la métrique**
|---|---|---|---|
| Utilisation du cache | Pourcentage | Moy, Max, Min | Pourcentage des ressources de cache allouées en cours d’utilisation par le cluster. Le cache désigne la taille du disque SSD allouée pour l’activité des utilisateurs conformément à la stratégie de cache définie. Une utilisation moyenne du cache de 80 % ou moins est un état acceptable pour un cluster. Si l’utilisation moyenne du cache est supérieure à 80 %, vous devez effectuer un [scale-up](manage-cluster-vertical-scaling.md) du cluster vers un niveau tarifaire à stockage optimisé, ou un [scale-out](manage-cluster-horizontal-scaling.md) afin d’ajouter des instances. Vous pouvez également adapter la stratégie de cache (moins de jours dans le cache). Si l’utilisation du cache est supérieure à 100 %, la taille des données à mettre en cache, conformément à la stratégie de mise en cache, est supérieure à la taille totale du cache sur le cluster. |
| UC | Pourcentage | Moy, Max, Min | Pourcentage des ressources de calcul allouées en cours d’utilisation par les ordinateurs du cluster. Une utilisation moyenne de l’UC de 80 % ou moins est acceptable pour un cluster. La valeur maximale d’utilisation de l’UC est de 100 %, ce qui signifie qu’il n’existe aucune ressource de calcul supplémentaire pour traiter les données. Quand les performances d’un cluster ne sont pas satisfaisantes, vérifiez la valeur maximale d’utilisation de l’UC afin de déterminer si des UC spécifiques sont bloquées. |
| Utilisation de l’ingestion | Pourcentage | Moy, Max, Min | Pourcentage des ressources réelles utilisées pour ingérer des données par rapport aux ressources totales allouées, dans la stratégie de capacité, pour effectuer l’ingestion. La stratégie de capacité par défaut est la suivante : pas plus de 512 opérations d’ingestion simultanées ou 75 % des ressources de cluster investies dans l’ingestion. Une utilisation moyenne de l’ingestion de 80 % ou moins est un état acceptable pour un cluster. La valeur maximale de l’utilisation d’ingestion est 100 %, ce qui signifie que toute la capacité d’ingestion de cluster est utilisée et qu’une file d’attente d’ingestion risque d’être créée. |
| Keep alive | Count | Avg | Effectue le suivi de la réactivité du cluster. Un cluster entièrement réactif retourne la valeur 1, et un cluster bloqué ou déconnecté retourne 0. |
| Nombre total de commandes limitées | Count | Moy, Max, Min, Somme | Nombre de commandes limitées (rejetées) dans le cluster, étant donné que le nombre maximal autorisé de commandes simultanées (parallèles) a été atteint. |
| Nombre total d’étendues | Count | Moy, Max, Min, Somme | Nombre total d’étendues de données dans le cluster. Les changements dans cette métrique peuvent impliquer des changements massifs de structures de données et une charge importante sur le cluster, car la fusion d’étendues de données est une activité gourmande en ressources processeur. |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Exporter les métriques d’intégrité et de performances

Exportez les métriques d’intégrité et de performances afin d’effectuer le suivi de l’intégrité générale et des performances des opérations d’exportation telles que le retard, les résultats, le nombre d’enregistrements et l’utilisation.

**Métrique** | **Unité** | **Agrégation** | **Description de la métrique**
|---|---|---|---|
Exportation continue : Nombre d’enregistrements exportés    | Count | Sum | Nombre total d’enregistrements exportés à partir du cluster. |
Minutes de retard max. pour l’exportation continue |    Count   | Max   | Valeur maximale en minutes des enregistrements exportés.|
Nombre d’exportations continues en attente | Count | Max   | Valeur maximale des opérations d’exportation en attente.
Résultat de l’exportation continue    | Count |   Count   | Nombre total d’opérations d’exportation continues, par résultat. La métrique comprend un nom et une base de données d’exportation continue. 
Utilisation de l’exportation |    Pourcentage | Max   | Utilisation de l’emplacement défini pour les opérations d’exportation.
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Métriques d’intégrité d’ingestion et de performances

Les métriques d’intégrité d’ingestion et de performances suivent l’intégrité et les performances générales des opérations d’ingestion telles que la latence, les résultats et le volume.

**Mesure** | **Unité** | **Agrégation** | **Description de la métrique**
|---|---|---|---|
| Événements traités (pour Event/IoT Hubs) | Count | Max, Min, Somme | Nombre total d’événements lus à partir de hubs d’événements et traités par le cluster. Les événements sont divisés en événements rejetés et en événements acceptés par le moteur de cluster. |
| Latence d’ingestion | Secondes | Moy, Max, Min | Latence des données ingérées, depuis la réception des données dans le cluster jusqu’à ce qu’elles soient prêtes à être interrogées. La période de latence d’ingestion varie en fonction du scénario d’ingestion. |
| Résultat de l’ingestion | Count | Count | Nombre total d’opérations d’ingestion ayant échoué et réussi. Utilisez **Appliquer la division** pour créer des compartiments de résultats de réussite et d’échec, et analyser les dimensions (**Valeur** > **État**).|
| Volume d’ingestion (en Mo) | Count | Max, Sum | Taille totale des données ingérées dans le cluster (en Mo) avant la compression. |
| | | | |  

### <a name="query-performance"></a>Performances des requêtes

Les métriques de performances des requêtes effectuent le suivi de la durée des requêtes et du nombre total de requêtes simultanées ou limitées.

**Mesure** | **Unité** | **Agrégation** | **Description de la métrique**
|---|---|---|---|
| Durée de la requête | Millisecondes | Moy, Min, Max, Somme | Durée totale jusqu’à réception des résultats de requête (n’inclut pas la latence du réseau). |
| Nombre total de demandes simultanées | Count | Moy, Max, Min, Somme | Nombre de requêtes exécutées en parallèle dans le cluster. Cette métrique est un bon moyen d’estimer la charge sur le cluster. |
| Nombre total de demandes limitées | Count | Moy, Max, Min, Somme | Nombre de requêtes limitées (rejetées) dans le cluster. Le nombre maximal de requêtes simultanées (parallèles) autorisées est défini dans la stratégie de requête simultanée. |
| | | | |

### <a name="streaming-ingest-metrics"></a>Métriques d’ingestion de streaming

Les métriques d’ingestion de streaming effectuent le suivi des données d’ingestion de streaming ainsi que du taux, de la durée et des résultats de requêtes.

**Mesure** | **Unité** | **Agrégation** | **Description de la métrique**
|---|---|---|---|
Taux de données d’ingestion en streaming |    Count   | RateRequestsPerSecond | Volume total de données ingérées dans le cluster. |
Durée de l’ingestion en streaming   | Millisecondes  | Moy, Max, Min | Durée totale de toutes les requêtes d’ingestion de streaming. |
Taux de demandes d’ingestion en streaming   | Count | Count, Moy, Max, Min, Somme | Nombre total de requêtes d’ingestion de streaming. |
Résultat de l’ingestion en streaming | Count | Avg   | Nombre total de requêtes d’ingestion de streaming par type de résultat. |
| | | | |

Informations supplémentaires concernant les [métriques de cluster Azure Data Explorer prises en charge](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)


## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Ingérer et interroger des données de supervision dans Azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Superviser les opérations d’ingestion d’Azure Data Explorer à l’aide des journaux de diagnostic](/azure/data-explorer/using-diagnostic-logs)
* [Démarrage rapide : Interroger des données dans Azure Data Explorer](web-query-data.md)
