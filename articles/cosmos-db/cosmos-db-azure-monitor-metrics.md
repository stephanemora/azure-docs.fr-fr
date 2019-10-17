---
title: Obtenir des métriques Azure Cosmos DB à partir d’Azure Monitor
description: Découvrez comment voir différentes catégories de métriques Azure Cosmos DB à partir d’Azure Monitor en utilisant le portail Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 905eca99c137af2fd40a1243de8fabd15314477c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973737"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Superviser et déboguer des métriques Azure Cosmos DB à partir d’Azure Monitor

Vous pouvez consulter des métriques Azure Cosmos DB à partir de l’API Azure Monitor. Azure Monitor offre plusieurs moyens d’interagir avec les métriques, notamment dans le portail Azure, en y accédant par le biais de l’API REST ou les interrogeant avec PowerShell ou l’interface CLI. Les métriques Azure Cosmos DB sont des valeurs numériques à latence faible, qui sont collectées toutes les minutes par défaut et que vous pouvez aussi agréger. Ces métriques peuvent prendre en charge des scénarios en temps réel.  

Cet article décrit les différentes métriques Azure Cosmos DB que vous pouvez voir à partir d’Azure Monitor en utilisant le portail Azure. Si les cas d’usage courants et la manière dont les métriques Azure Cosmos DB sont utilisées pour analyser et déboguer ces problèmes, consultez l’article [Superviser et déboguer à l’aide de métriques dans Azure Cosmos DB](use-metrics.md). Vous allez utiliser l’un de vos comptes Azure Cosmos existants pour voir les différentes métriques au niveau de la base de données, du conteneur, de la région, de la requête ou de l’opération. Ainsi, veillez à disposer d’un compte Azure Cosmos avec des exemples de données et effectuez des opérations CRUD sur ces données.

## <a name="view-metrics-from-azure-portal"></a>Voir les métriques depuis le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Sélectionnez **Surveillance** dans la barre de navigation gauche, puis sélectionnez **Métriques**.

   ![Volet Métriques dans Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. À partir du volet **Métriques** > **Sélectionner une ressource** > choisissez l’**abonnement** exigé, puis **Groupe de ressources**. Pour le **type de ressource**, sélectionnez **Comptes Azure Cosmos DB**, choisissez une de vos comptes Azure Cosmos existants, puis sélectionnez **Appliquer**. 

   ![Choisir un compte Cosmos DB pour voir les métriques](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Ensuite, vous pouvez sélectionner une métrique dans la liste des métriques disponibles. Vous pouvez sélectionner des métriques propres aux unités de requête, au stockage, à la latence, à la disponibilité, à Cassandra, etc. Pour découvrir de plus près toutes les métriques disponibles dans cette liste, consultez la section [Métriques par catégorie](#metrics-by-category) de cet article. Dans cet exemple, nous allons sélectionner **Unités de requête** et **Moy** comme valeur d’agrégation. 

   En plus de ces détails, vous pouvez également sélectionner l’**intervalle de temps** et la **granularité temporelle** des métriques. Au maximum, vous pouvez voir les métriques des 30 derniers jours.  Une fois que vous avez appliqué le filtre, un graphique s’affiche. Vous pouvez voir le nombre moyen d’unités de requête consommées par minute pendant la période sélectionnée.  

   ![Choisir une métrique à partir du portail Azure](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Ajouter des filtres aux métriques

Vous pouvez également filtrer les métriques et le graphique affiché par une valeur **CollectionName**, **DatabaseName**, **OperationType**, **Region** et **StatusCode** spécifique. Pour filtrer les métriques, sélectionnez **Ajouter un filtre** et choisissez la propriété nécessaire comme **OperationType**, puis sélectionnez une valeur comme **Requête**. Le graphique affiche alors les unités de requête consommées pour l’opération de requête pendant la période sélectionnée. Les opérations exécutées par procédure stockée ne sont pas journalisées si bien qu’elles ne sont pas disponibles sous la métrique OperationType.

![Ajouter un filtre pour sélectionner la granularité des métriques](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

Vous pouvez regrouper des métriques à l’aide de l’option **Appliquer la division**. Par exemple, vous pouvez regrouper les unités de requête par type d’opération et voir le graphique pour toutes les opérations comme illustré dans l’image suivante : 

![Ajouter un filtre d’application de la division](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Métriques par catégorie

### <a name="request-metrics"></a>Métriques de demande
            
|Métrique (Nom d’affichage de la métrique)|Unité (Type d’agrégation) |Description|Dimensions| Granularités de temps| Mappage de métrique héritée | Usage |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Nombre total de requêtes) | Count (Nombre) | Nombre de requêtes effectuées| DatabaseName, CollectionName, Region, StatusCode| Tous | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Erreur interne du service, Service indisponible, Requêtes limitées, Requêtes moyennes par seconde | Permet de surveiller les requêtes par code d'état, conteneur à une granularité d'une minute. Pour obtenir les requêtes moyennes par seconde, utilisez l'agrégation Count pour une minute et divisez-la par 60. |
| MetadataRequests (Requêtes de métadonnées) |Count (Nombre) | Nombre de demandes de métadonnées. Azure Cosmos DB gère le conteneur des métadonnées système pour chaque compte, ce qui vous permet d’énumérer les collections, les bases de données, etc., ainsi que leur configuration, et ce gratuitement. | DatabaseName, CollectionName, Region, StatusCode| Tous| |Permet de surveiller les limitations dues aux requêtes de métadonnées.|
| MongoRequests (Requêtes Mongo) | Count (Nombre) | Nombre de requêtes Mongo effectuées | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tous |Mongo Query Request Rate, Mongo Update Request Rate, Mongo Delete Request Rate, Mongo Insert Request Rate, Mongo Count Request Rate| Permet de surveiller les erreurs de requête Mongo, les utilisations par type de commande. |

### <a name="request-unit-metrics"></a>Métriques d’unités de requête

|Métrique (Nom d’affichage de la métrique)|Unité (Type d’agrégation)|Description|Dimensions| Granularités de temps| Mappage de métrique héritée | Usage |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Frais des requêtes Mongo) | Count (Total) |Unités de requête Mongo consommées| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Tous |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Permet de surveiller les unités de requête des ressources Mongo en une minute.|
| TotalRequestUnits (Nombre total d’unités de requête)| Count (Total) | Unités de requête consommées| DatabaseName, CollectionName, Region, StatusCode |Tous| TotalRequestUnits| Permet de surveiller l’utilisation total des unités de requête à une granularité d’une minute. Pour obtenir les unités de requête moyennes consommées par seconde, utilisez l'agrégation Total pour une minute et divisez-la par 60.|
| ProvisionedThroughput (Débit provisionné)| Count (Maximum) |Débit approvisionné à la granularité du conteneur| DatabaseName, ContainerName| 5 Mo| | Permet de surveiller le débit approvisionné par conteneur.|

### <a name="storage-metrics"></a>Métriques de stockage

|Métrique (Nom d’affichage de la métrique)|Unité (Type d’agrégation)|Description|Dimensions| Granularités de temps| Mappage de métrique héritée | Usage |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (Stockage disponible) |Octets (Total) | Stockage total disponible signalé à une granularité de 5 minutes par région| DatabaseName, CollectionName, Region| 5 Mo| Stockage disponible| Permet de surveiller la capacité de stockage disponible (applicable uniquement aux collections de stockage fixe) La granularité minimale doit être de 5 minutes.| 
| DataUsage (Utilisation des données) |Octets (Total) |Utilisation totale des données signalée à une granularité de 5 minutes par région| DatabaseName, CollectionName, Region| 5 Mo |Taille des données | Permet de surveiller l'utilisation totale des données d'un conteneur et d'une région, la granularité minimale doit être de 5 minutes.|
| IndexUsage (Utilisation d’index) | Octets (Total) |Utilisation d’index totale signalée à une granularité de 5 minutes par région| DatabaseName, CollectionName, Region| 5 Mo| Taille d'index| Permet de surveiller l'utilisation totale des données d'un conteneur et d'une région, la granularité minimale doit être de 5 minutes. |
| DocumentQuota (Quota de document) | Octets (Total) | Quota de stockage total signalé à une granularité de 5 minutes par région.| DatabaseName, CollectionName, Region| 5 Mo |Capacité de stockage| Permet de surveiller le quota total des données d'un conteneur et d'une région, la granularité minimale doit être de 5 minutes.|
| DocumentCount (Nombre de documents) | Count (Total) |Nombre total de documents signalé à une granularité de 5 minutes par région| DatabaseName, CollectionName, Region| 5 Mo |Nombre de documents|Permet de surveiller le nombre de documents d'un conteneur et d'une région, la granularité minimale doit être de 5 minutes.|

### <a name="latency-metrics"></a>Métriques de latence

|Métrique (Nom d’affichage de la métrique)|Unité (Type d’agrégation)|Description|Dimensions| Granularités de temps| Usage |
|---|---|---|---| ---| ---|
| ReplicationLatency (Latence de réplication)| Millisecondes (Minimum, Maximum, Moyenne) | Latence de réplication P99 des régions source et cible pour le compte géolocalisé| SourceRegion, TargetRegion| Tous | Permet de surveiller la latence de réplication P99 entre deux régions pour un compte géorépliqué. |


### <a name="availability-metrics"></a>Métriques de disponibilité

|Métrique (Nom d’affichage de la métrique) |Unité (Type d’agrégation)|Description| Granularités de temps| Mappage de métrique héritée | Usage |
|---|---|---|---| ---| ---|
| ServiceAvailability (Disponibilité du Service)| Pour cent (Minimum, Maximum) | Disponibilité des requêtes de compte à une granularité d’une heure| 1 h | Disponibilité des services | Représente le pourcentage des requêtes totales passées. Une requête échoue en raison d'une erreur système si le code d'état correspond à 410, 500 ou 503 Permet de surveiller la disponibilité du compte à une granularité d'une heure. |


### <a name="cassandra-api-metrics"></a>Métriques de l'API Cassandra

|Métrique (Nom d’affichage de la métrique)|Unité (Type d’agrégation)|Description|Dimensions| Granularités de temps| Usage |
|---|---|---|---| ---| ---|
| CassandraRequests (Requêtes Cassandra) | Count (Nombre) | Nombre de requêtes d’API Cassandra effectuées| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Tous| Permet de surveiller les requêtes Cassandra à une granularité d’une minute. Pour obtenir les requêtes moyennes par seconde, utilisez l'agrégation Count pour une minute et divisez-la par 60.|
| CassandraRequestCharges (Frais des requêtes Cassandra) | Count (Sum, Min, Max, Avg) | Unités de requête consommées par les requêtes d’API Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Tous| Permet de surveiller les unités de requête utilisées par minute par un compte d’API Cassandra.|
| CassandraConnectionClosures (Fermetures de connexion Cassandra) |Count (Nombre) |Nombre de connexions Cassandra fermées| ClosureReason, Region| Tous | Permet de surveiller la connectivité entre les clients et l’API Cassandra Azure Cosmos DB.|

## <a name="next-steps"></a>Étapes suivantes

* [Voir et superviser les métriques à partir du volet des métriques de compte Azure Cosmos DB](use-metrics.md)

* [Journalisation des diagnostics dans Azure Cosmos DB](logging.md)
