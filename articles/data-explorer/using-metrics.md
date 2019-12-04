---
title: Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques
description: Découvrez comment utiliser les métriques Azure Data Explorer pour superviser les performances, l’intégrité et l’utilisation du cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: f5b47a5ae9d13711233d0e4852ec487af7344622
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173791"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster et une ou plusieurs bases de données dans ce cluster. Ensuite, ingérez (chargez) des données dans une base de données pour pouvoir exécuter des requêtes dessus. Les métriques Azure Data Explorer fournissent des indicateurs clés concernant l’intégrité et les performances des ressources de cluster. Utilisez les métriques détaillées dans cet article pour superviser l’intégrité et les performances du cluster Azure Data Explorer dans votre scénario spécifique en tant que métriques autonomes. Vous pouvez également utiliser des métriques comme base pour les [tableaux de bord Azure](/azure/azure-portal/azure-portal-dashboards) et les [alertes Azure](/azure/azure-monitor/platform/alerts-metric-overview) operationnels.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas encore d’abonnement Azure, [créez un compte Azure gratuit](https://azure.microsoft.com/free/)

* Créez [un cluster et une base de données](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="using-metrics"></a>Utilisation de métriques

Dans votre cluster Azure Data Explorer, sélectionnez **Métriques** pour ouvrir le volet de métriques et commencer l’analyse sur votre cluster.

![Sélection de l’option Métriques](media/using-metrics/select-metrics.png)

Dans le volet Métriques :

![Volet Métriques](media/using-metrics/metrics-pane.png)

1. Pour créer un graphique de métriques, sélectionnez le nom de la **Métrique** et l’**agrégation** pertinente par métrique comme indiqué ci-dessous. Les sélecteurs **Ressource** et **Espace de noms de métrique**  sont présélectionnés pour votre cluster Azure Data Explorer.

    **Mesure** | **Unité** | **Agrégation** | **Description de la métrique**
    |---|---|---|---|
    | Utilisation du cache | Pourcentage | Moy, Max, Min | Pourcentage des ressources de cache allouées en cours d’utilisation par le cluster. Le cache fait référence à la taille du disque SSD allouée pour l’activité des utilisateurs conformément à la stratégie de cache définie. Une utilisation moyenne du cache de 80 % ou moins est un état acceptable pour un cluster. Si l’utilisation moyenne du cache est supérieure à 80 %, vous devez effectuer un [scale-up](manage-cluster-vertical-scaling.md) du cluster vers un niveau tarifaire à stockage optimisé, ou un [scale-out](manage-cluster-horizontal-scaling.md) afin d’ajouter des instances. Vous pouvez également adapter la stratégie de cache (moins de jours dans le cache). Si l’utilisation du cache est supérieure à 100 %, la taille des données à mettre en cache, conformément à la stratégie de mise en cache, est supérieure à la taille totale du cache sur le cluster. |
    | UC | Pourcentage | Moy, Max, Min | Pourcentage des ressources de calcul allouées en cours d’utilisation par les ordinateurs du cluster. Une utilisation moyenne de l’UC de 80 % ou moins est acceptable pour un cluster. La valeur maximale d’utilisation de l’UC est de 100 %, ce qui signifie qu’il n’existe aucune ressource de calcul supplémentaire pour traiter les données. Quand les performances d’un cluster ne sont pas satisfaisantes, vérifiez la valeur maximale d’utilisation de l’UC afin de déterminer si des UC spécifiques sont bloquées. |
    | Événements traités (pour Event Hubs) | Count | Max, Min, Somme | Nombre total d’événements lus à partir de hubs d’événements et traités par le cluster. Les événements sont divisés en événements rejetés et en événements acceptés par le moteur de cluster. |
    | Latence d’ingestion | Secondes | Moy, Max, Min | Latence des données ingérées, depuis la réception des données dans le cluster jusqu’à ce qu’elles soient prêtes à être interrogées. La période de latence d’ingestion varie en fonction du scénario d’ingestion. |
    | Résultat de l’ingestion | Count | Count | Nombre total d’opérations d’ingestion ayant échoué et réussi. Utilisez **Appliquer la division** pour créer des compartiments de résultats de réussite et d’échec, et analyser les dimensions (**Valeur** > **État**).|
    | Utilisation de l’ingestion | Pourcentage | Moy, Max, Min | Pourcentage des ressources réelles utilisées pour ingérer des données par rapport aux ressources totales allouées, dans la stratégie de capacité, pour effectuer l’ingestion. La stratégie de capacité par défaut est la suivante : pas plus de 512 opérations d’ingestion simultanées ou 75 % des ressources de cluster investies dans l’ingestion. Une utilisation moyenne de l’ingestion de 80 % ou moins est un état acceptable pour un cluster. La valeur maximale de l’utilisation d’ingestion est 100 %, ce qui signifie que toute la capacité d’ingestion de cluster est utilisée et qu’une file d’attente d’ingestion risque d’être créée. |
    | Volume d’ingestion (en Mo) | Count | Max, Min, Somme | Taille totale des données ingérées dans le cluster (en Mo) avant la compression. |
    | Keep alive | Count | Moy | Effectue le suivi de la réactivité du cluster. Un cluster entièrement réactif retourne la valeur 1, et un cluster bloqué ou déconnecté retourne 0. |
    | Durée de la requête | Secondes | Nombre, Moy, Min, Max, Somme | Durée totale jusqu’à réception des résultats de requête (n’inclut pas la latence du réseau). |
    | | | |

    Informations supplémentaires concernant les [métriques Azure Data Explorer prises en charge](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Sélectionnez le bouton **Ajouter une métrique** pour afficher le tracé de plusieurs métriques sur le même graphique.
3. Sélectionnez le bouton **+ Nouveau graphique** pour afficher plusieurs graphiques dans une même vue.
4. Utilisez le sélecteur de temps pour modifier l’intervalle de temps (par défaut : les dernières 24 heures).
5. Utilisez [**Ajouter un filtre** et **Appliquer la division**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) pour les métriques qui ont des dimensions.
6. Sélectionnez **Épingler au tableau de bord** pour ajouter la configuration de votre graphique aux tableaux de bord afin de pouvoir le visualiser à nouveau.
7. Définissez **Nouvelle règle d’alerte** pour visualiser vos métriques à l’aide des critères définis. La nouvelle règle d’alerte inclura les dimensions de la ressource, de la métrique, du fractionnement et du filtre cibles de votre graphique. Modifiez ces paramètres dans le [volet de création de règle d’alerte](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Informations supplémentaires sur l’utilisation de [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Ingérer et interroger des données de supervision dans Azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Superviser les opérations d’ingestion d’Azure Data Explorer à l’aide des journaux de diagnostic](/azure/data-explorer/using-diagnostic-logs)
* [Démarrage rapide : Interroger des données dans Azure Data Explorer](web-query-data.md)
