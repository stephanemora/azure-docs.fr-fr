---
title: Surveiller les performances, l’intégrité et l’utilisation avec des mesures Explorateur de données Azure
description: Découvrez comment utiliser les mesures de l’Explorateur de données Azure pour surveiller les performances du cluster, l’intégrité et l’utilisation.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: 5252ca8898439b63a8819f6abfd634de0786932b
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851804"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Surveiller les performances, l’intégrité et l’utilisation avec des mesures Explorateur de données Azure

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster et une ou plusieurs bases de données dans ce cluster. Ensuite, ingérez (chargez) des données dans une base de données pour pouvoir exécuter des requêtes dessus. Les métriques de l’Explorateur de données Azure fournissent des indicateurs clés concernant l’intégrité et les performances des ressources de cluster. Utilisez les mesures qui sont détaillées dans cet article pour surveiller l’intégrité du cluster de l’Explorateur de données Azure et les performances dans votre scénario spécifique des mesures autonomes. Vous pouvez également utiliser des mesures comme base pour operational [des tableaux de bord Azure](/azure/azure-portal/azure-portal-dashboards) et [Azure Alerts](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Conditions préalables

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/).

* Créer un [cluster et la base de données](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="using-metrics"></a>À l’aide de mesures

Dans votre cluster de l’Explorateur de données Azure, sélectionnez **métriques** pour ouvrir le volet mesures et de commencer l’analyse sur votre cluster.

![Sélection de l’option Métriques](media/using-metrics/select-metrics.png)

Dans le volet mesures :

![Volet de mesures](media/using-metrics/metrics-pane.png)

1. Pour créer un graphique de mesures, sélectionnez **métrique** nom et pertinentes **agrégation** par métrique comme indiqué ci-dessous. Le **ressource** et **métrique Namespace** sélecteurs sont présélectionnées dans votre cluster de l’Explorateur de données Azure.

    **Mesure** | **Unité** | **Agrégation** | **Description de la métrique**
    |---|---|---|---|
    | Utilisation du cache | Pourcentage | AVG, Max, Min | Rapport entre la taille du cache (en fonction de la stratégie de cache définie) et la taille totale du cache du cluster (taille totale du disque SSD pour l’activité des utilisateurs). Une utilisation du cache moyen de 80 % ou moins est un état durable pour un cluster. Si l’utilisation du cache moyenne est supérieure à 80 %, le cluster doit être [mis à l’échelle](manage-cluster-scale-up.md) vers un stockage optimisé le niveau tarifaire ou [mis à l’échelle](manage-cluster-scale-out.md) à plusieurs instances. Vous pouvez également adapter la stratégie de cache (moins de jours dans le cache). Si l’utilisation du cache est supérieure à 100 %, la taille des données doit être mis en cache, conformément à la stratégie de mise en cache, est plus grande que la taille totale du cache sur le cluster. |
    | UC | Pourcentage | AVG, Max, Min | Rapport entre le total utilisé ou UC disponible sur l’ensemble du cluster. Un moyenne du processeur de 80 % ou moins est acceptable pour un cluster. La valeur maximale du processeur est de 100 %, ce qui signifie qu’aucune ressource de calcul supplémentaires pour traiter les données. Lorsqu’un cluster n’est pas fonctionne correctement, vérifiez la valeur maximale du processeur afin de déterminer s’il existe des unités centrales spécifiques qui sont bloqués. |
    | Événements traités (concentrateurs d’événements) | Nombre | Max, Min, Sum | Nombre total d’événements envoyés par Event Hubs et reçus par le cluster. Les événements sont fractionnés en événements rejetées et les événements acceptés par le moteur de cluster. |
    | Latence d’ingestion | Secondes | AVG, Max, Min | Latence des données reçues à partir du moment de que la réception des données dans le cluster jusqu'à ce qu’il soit prêt à être interrogé. Latence d’ingestion est exprimée en secondes. La période de latence d’Ingestion varie selon le scénario d’ingestion. |
    | Résultat de l’ingestion | Nombre | Nombre | Nombre total d’opérations d’ingestion qui a échoué et a réussi. Utilisez **appliquer fractionnement** pour créer des compartiments de réussite et résultats des tests.|
    | Utilisation de l’ingestion | Pourcentage | AVG, Max, Min | Rapport entre les ressources réelles utilisées pour recevoir les données et les total des ressources allouées dans la stratégie de capacité pour effectuer l’ingestion. La stratégie de capacité par défaut n’est pas plus de 512 opérations simultanées d’ingestion ou de 75 % des ressources de cluster investis dans l’ingestion. Utilisation de l’ingestion moyenne de 80 % ou moins est un état durable pour un cluster. Valeur maximale de l’utilisation d’ingestion est 100 %, ce qui signifie que toutes les possibilité d’ingestion de cluster est utilisée et une file d’attente de l’ingestion peut aboutir. |
    | Volume d’ingestion (en Mo) | Nombre | Max, Min, Sum | La taille totale des données ingérées dans le cluster (en Mo). Les unités sont au nombre de mégaoctets de données ingérées avant compression. |
    | Keep alive | Nombre | Moy | Effectue le suivi de la réactivité du cluster. Un cluster entièrement réactif retourne la valeur 1 et un cluster bloqué ou déconnecté retourne 0. |
    | Durée de la requête | Secondes | Count, Avg, Min, Max, Sum | Durée totale jusqu'à la réception des résultats de la requête. |
    | | | |

    Des informations supplémentaires concernant [pris en charge les métriques du cluster Explorateur de données Azure](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Sélectionnez le **ajouter une métrique** bouton pour afficher plusieurs mesures tracées dans le même graphique.
3. Sélectionnez le **+ nouveau graphique** bouton pour afficher plusieurs graphiques dans une même vue.
4. Utilisez le sélecteur de temps pour modifier l’intervalle de temps (par défaut : dernières 24 heures).
5. Utilisez [ **ajouter un filtre** et **appliquer fractionnement** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) pour les mesures qui ont des dimensions.
6. Sélectionnez **épingler au tableau de bord** pour ajouter la configuration de votre graphique aux tableaux de bord afin que vous puissiez l’afficher à nouveau.
7. Définissez **nouvelle règle d’alerte** pour visualiser vos mesures à l’aide de l’ensemble de critères. La nouvelle règle d’alerte inclura les dimensions de la ressource, de la métrique, du fractionnement et du filtre cibles de votre graphique. Modifier ces paramètres dans le [volet de la création d’une règle d’alerte](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Des informations supplémentaires sur l’utilisation de la [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Interroger des données dans Azure Data Explorer](web-query-data.md)
