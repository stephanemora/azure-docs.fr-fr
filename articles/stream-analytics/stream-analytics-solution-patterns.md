---
title: Modèles de solution Stream Analytique Azure
description: Découvrez les modèles de solution pour Azure Stream Analytique.
author: zhongc
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 80843abe130f1388a5d4081adab7b9128446763b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761989"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Modèles de solution Stream Analytique Azure

Comme de nombreux autres services dans Azure, Stream Analytique est surtout avec d’autres services pour créer une solution de bout en bout plus volumineuse. Cet article décrit les solutions d’Analytique de Stream Azure simples et différents modèles d’architecture. Vous pouvez créer ces modèles pour développer des solutions plus complexes. Les modèles décrits dans cet article peuvent être utilisés dans un large éventail de scénarios. Exemples de modèles spécifiques de scénario sont disponibles sur [architectures de solution Azure](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-with-a-real-time-dashboard"></a>Créer un travail d’Analytique de Stream avec un tableau de bord en temps réel

Simplicité des Analytique Azure Stream d’utilisation, vous pouvez rapidement mettre en des alertes et des tableaux de bord en temps réel. Une solution simple et reçoit des événements à partir d’Event Hubs ou IoT Hub, et [le tableau de bord Power BI avec un jeu de données de diffusion en continu de flux](/power-bi/service-real-time-streaming). Pour plus d’informations, consultez le didacticiel détaillé [analyser les données d’appel téléphonique avec Analytique de Stream et visualiser les résultats dans le tableau de bord Power BI](stream-analytics-manage-job.md).

![Tableau de bord ASA Power BI](media/stream-analytics-solution-patterns/pbidashboard.png)

Cette solution peut être générée dans quelques minutes seulement à partir du portail Azure. Il est que sans beaucoup de codage impliquées et langage SQL est utilisé pour exprimer la logique métier.

Ce modèle de solution du tableau de bord en temps réel offre la plus faible latence à partir de la source d’événements au tableau de bord Power BI dans un navigateur. Azure Stream Analytique est le seul service Azure avec cette fonctionnalité intégrée.

## <a name="use-sql-for-dashboard"></a>Utiliser SQL pour le tableau de bord

Le tableau de bord Power BI offre une latence faible, mais il ne peut pas être utilisé pour produire des rapports Power BI soit-il complètes. Un modèle de création de rapports courant consiste à tout d’abord vos données à une base de données SQL de sortie. Ensuite utiliser le connecteur SQL de Power BI à la requête SQL pour les données les plus récentes.

![Tableau de bord ASA SQL](media/stream-analytics-solution-patterns/sqldashboard.png)

À l’aide de SQL database vous donne plus de souplesse au détriment d’une latence plus élevée. Cette solution est optimale pour les tâches avec des conditions de latence supérieures à une seconde. Avec cette méthode, vous pouvez optimiser l’utilitaire de Power BI à une tranche supplémentaire et manipulent les données pour les rapports. Vous bénéficiez également de la souplesse d’utilisation d’autres solutions de tableau de bord comme Tableau.

SQL n’est pas un magasin de données d’un débit élevé, et le débit maximal pour une base de données SQL à partir d’Azure Stream Analytique est 24 Mo/s. Si les sources d’événements dans votre solution produisent des données à une fréquence plus élevée, vous devez utiliser la logique de traitement dans Stream Analytique afin de réduire le taux de sortie SQL. Une correspondance avec des jointures temporelles de techniques telles que le filtrage, les agrégats fenêtrés, modèle et les fonctions analytiques peuvent être utilisées. Le taux de sortie à SQL permettre être optimisé à l’aide des techniques décrites dans [sortie Azure Stream Analytique pour Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Incorporer des informations en temps réel dans votre application avec la messagerie d’événements

L’utilisation ensuite plus populaire de Stream Analytique consiste à générer des alertes en temps réel. Dans ce modèle de solution, une logique métier dans Stream Analytique peut être utilisée pour détecter [modèles temporelles et spatiales](stream-analytics-geospatial-functions.md) ou [anomalies](stream-analytics-machine-learning-anomaly-detection.md), puis produire signaux d’alerte. Toutefois, contrairement à la solution de tableau de bord où Stream Analytique utilise Power BI comme un point de terminaison par défaut, un nombre de récepteurs de données intermédiaires permettre servir. Ces récepteurs incluent Event Hubs, Service Bus et Azure Functions. Vous, en tant que le Générateur d’applications, devez décider quel récepteur de données convient le mieux à votre scénario.

Logique de consommateur d’événements en aval doit être implémentée pour générer des alertes dans votre workflow d’entreprise existant. Étant donné que vous pouvez implémenter une logique personnalisée dans Azure Functions, fonctions est le moyen le plus rapide, vous pouvez effectuer cette intégration. Un didacticiel pour l’utilisation de la fonction Azure se trouve dans la sortie pour un travail Stream Analytique [exécuter Azure Functions à partir des travaux d’Azure Stream Analytique](stream-analytics-with-azure-functions.md). Azure Functions prend également en charge divers types de notifications, y compris le texte et messagerie. Application logique peut également être utilisée pour cette intégration, avec les concentrateurs d’événements entre Stream Analytique et d’application logique.

![Application de messagerie d’événements ASA](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Event Hubs, propose quant à eux, le point d’intégration plus souple. Nombreux autres services, tels que l’Explorateur de données Azure et Time Series Insight, peuvent consommer des événements à partir d’Event Hubs. Services peuvent être connectés directement au récepteur Event Hubs à partir d’Azure Stream Analytique pour compléter la solution. Event Hubs est également le plus haut débit broker de messagerie disponible sur Azure pour ces scénarios d’intégration.

## <a name="dynamic-applications-and-websites"></a>Sites Web et des applications dynamiques

Vous pouvez créer des visualisations personnalisées en temps réel, tels que tableau de bord ou la visualisation, de carte à l’aide d’Azure Stream Analytique et Service Azure SignalR. Les clients web à l’aide de SignalR, peuvent être mis à jour et affichent le contenu dynamique en temps réel.

![Application dynamique ASA](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Incorporer des informations en temps réel dans votre application par le biais des magasins de données

La plupart des services web et applications web utilisent aujourd'hui un modèle demande/réponse pour servir de la couche de présentation. Le modèle demande/réponse est simple à créer et peut être facilement mis à l’échelle avec des temps de réponse faible à l’aide d’un serveur frontal sans état et évolutives magasins, comme Cosmos DB.

Volume de données haute crée souvent les goulots d’étranglement dans un système CRUD. Le [approvisionnement en modèle de solution événements](/azure/architecture/patterns/event-sourcing) est utilisé pour résoudre les goulots d’étranglement de performances. Insights et modèles temporels sont également difficile et inefficace à extraire d’une banque de données traditionnel. Piloté par les applications souvent à des données de haut volume modernes adoptent une architecture basée sur les flux de données. Azure Stream Analytique en tant que le moteur de calcul pour les données en mouvement est un "cheville ouvrière" de cette architecture.

![Application d’événements d’approvisionnement ASA](media/stream-analytics-solution-patterns/eventsourcingapp.png)

Dans ce modèle de solution, les événements sont traités et agrégées dans des magasins de données par Azure Stream Analytique. La couche d’application interagit avec les magasins de données à l’aide du modèle de demande/réponse traditionnel. En raison de capacité d’Analytique de le Stream pour traiter un grand nombre d’événements en temps réel, l’application est hautement évolutive sans devoir en bloc de la couche de magasin de données. La couche de magasin de données est essentiellement une vue matérialisée dans le système. [Sortie de Stream Analytique Azure à Azure Cosmos DB](stream-analytics-documentdb-output.md) décrit l’utilisation de Cosmos DB comme une sortie Stream Analytique.

Dans les applications réelles où la logique de traitement est complexe et il est nécessaire de mettre à niveau certaines parties de la logique de manière indépendante, plusieurs travaux d’Analytique de Stream peuvent être composées avec Event Hubs en tant qu’intermédiaire event broker.

![Application de source d’événements complexes ASA](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Ce modèle améliore la résilience et la facilité de gestion du système. Toutefois, même si Stream Analytique garantit exactement une fois le traitement, il existe une faible probabilité pour que les événements en double peuvent se retrouver dans les Hubs d’événements intermédiaire. Il est important pour le travail d’Analytique de Stream en aval pour la déduplication d’événements à l’aide de clés de logique dans une fenêtre de la boucle. Pour plus d’informations sur la livraison d’événements, consultez [garanties de remise d’événement](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) référence.

## <a name="use-reference-data-for-application-customization"></a>Utiliser les données de référence pour la personnalisation de l’application

La fonctionnalité de données de référence Azure Stream Analytique est conçue spécifiquement pour la personnalisation de l’utilisateur final telles que la génération d’alertes de seuil, le traitement des règles, et [clôture virtuelle](geospatial-scenarios.md). La couche application peut accepter les modifications apportées aux paramètres et les stocker dans une base de données SQL. Le travail Stream Analytique interroge les modifications à partir de la base de données régulièrement et rend les paramètres de personnalisation accessible via une jointure de données de référence. Pour plus d’informations sur l’utilisation des données de référence pour la personnalisation de l’application, consultez [les données de référence SQL](sql-reference-data.md) et [jointure des données de référence](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Ce modèle peut également être utilisé pour implémenter un moteur de règles où les seuils des règles sont définies à partir des données de référence. Pour plus d’informations sur les règles, consultez [traiter les règles de seuil configurables dans Azure Stream Analytique](stream-analytics-threshold-based-rules.md).

![Application de données de référence ASA](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Ajouter la Machine Learning pour vos analyses en temps réel

Intégrée des Azure Analytique Stream [modèle de détection d’anomalie](stream-analytics-machine-learning-anomaly-detection.md) est un moyen pratique d’introduire un apprentissage à votre application en temps réel. Pour les besoins d’une plage plus large de Machine Learning, consultez [Analytique de Stream Azure s’intègre avec le service de calcul de score Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Pour les utilisateurs expérimentés qui souhaitent intégrer la formation en ligne et la notation dans le même pipeline d’Analytique de Stream, consultez cet exemple de comment procéder avec [régression linéaire](stream-analytics-high-frequency-trading.md).

![Application de Machine Learning ASA](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Près de l’entreposage de données en temps réel

Un autre modèle courant est en temps réel entrepôts de données, également appelée entrepôt de données de diffusion en continu. Outre les événements arrivant dans Event Hubs et IoT Hub à partir de votre application, [Azure Stream Analytique est en cours d’exécution sur IoT Edge](stream-analytics-edge.md) peut être utilisé pour remplir le nettoyage des données, de la réduction des données et de la banque de données et besoins vers l’avant. Analytique de Stream en cours d’exécution sur IoT Edge peut gérer correctement les problèmes de connectivité et de limitation de bande passante dans le système. L’adaptateur de sortie SQL peut être utilisé pour la sortie vers SQL Data Warehouse ; Toutefois, le débit maximal est limité à 10 Mo/s.

![Entreposage de données ASA](media/stream-analytics-solution-patterns/datawarehousing.png)

La première consiste à améliorer le débit avec certains compromis de latence d’archiver les événements dans le stockage Blob Azure, puis [les importer dans SQL Data Warehouse avec Polybase](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md). Vous devez assembler manuellement la sortie à partir de l’Analytique de Stream dans le stockage blob et entrée depuis le stockage blob vers SQL Data Warehouse par [l’archivage des données par horodatage](stream-analytics-custom-path-patterns-blob-storage-output.md) et l’importation régulièrement.

Dans ce modèle d’utilisation, Azure Stream Analytique est utilisé comme un moteur ETL quasiment en temps réel. Qui vient d’être des événements entrants sont transformés en permanence et stockées pour la consommation du service analytique en aval.

![Débit élevé ASA entreposage de données](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>L’archivage des données en temps réel pour l’analytique

La plupart des activités de science et l’analytique des données arrivent toujours hors connexion. Données peuvent être archivées par Azure Stream Analytique via Azure Data Lake Store Gen2 sortie et les formats de sortie Parquet. Cette fonctionnalité supprime la friction à un flux de données directement dans Azure Data Lake Analytique, Azure Databricks et Azure HDInsight. Azure Stream Analytique est utilisé en tant que quasiment en temps réel ETL moteur dans cette solution. Vous pouvez explorer les données archivées dans Data Lake à l’aide de différents moteurs de calcul.

![Analytique hors connexion ASA](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Utiliser les données de référence pour l’enrichissement

Enrichissement des données sont souvent requis pour les moteurs ETL. Azure Stream Analytique prend en charge l’enrichissement des données avec [données de référence](stream-analytics-use-reference-data.md) à partir de la base de données SQL et stockage Blob Azure. Enrichissement des données peuvent être effectuée pour les données de destination dans Azure Data Lake et SQL Data Warehouse.

![Analytique de hors connexion ASA avec enrichissement des données](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Faire fonctionner des informations à partir des données archivées

Si vous combinez le modèle analytique hors connexion avec l’application en temps réel proche modèle, vous pouvez créer une boucle de rétroaction. La boucle de rétroaction permet l’application d’ajuster automatiquement pour la modification des modèles dans les données. Cette boucle de commentaires peut être aussi simple que la modification de la valeur de seuil d’alerte, ou aussi complexe que REFORMATION des modèles d’apprentissage. La même architecture de solution peut être appliquée pour les deux tâches ASA en cours d’exécution dans le cloud et sur IoT Edge.

![Opérationnalisation d’insights ASA](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Comment surveiller les travaux ASA

Un travail Azure Stream Analytique peut être exécuté 24/7 pour traiter les événements entrants en continu en temps réel. Son temps de disponibilité garanti est cruciale pour l’intégrité de l’application globale. Alors que Stream Analytique est le seul service analytique de diffusion en continu dans l’industrie qui offre un [garantie de disponibilité de 99,9 %](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/), vous risquez de subir toujours certain niveau de temps d’arrêt. Au fil des années, Stream Analytique a introduit des mesures, les journaux et les États de travail pour refléter l’intégrité des travaux. Toutes les sont exposés via le service Azure Monitor et peuvent être exportés davantage à OMS. Pour plus d’informations, consultez [Analytique de Stream comprendre comment surveiller les requêtes et surveillance du travail](stream-analytics-monitoring.md).

![Surveillance d’ASA](media/stream-analytics-solution-patterns/monitoring.png)

Il existe deux éléments clés à surveiller :

- [État d’échec du travail](job-states.md)

    Tout d’abord, vous devez vous assurer que le travail est en cours d’exécution. Sans le travail en cours d’exécution, aucune nouvelle métriques ou les journaux ne sont générés. Travaux peuvent changer à un état d’échec pour diverses raisons, notamment un haut niveau de l’utilisation de SU (c'est-à-dire manquer de ressources).

- [Métriques de délai de filigrane](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Cette mesure reflète le retard de traitement de votre pipeline est dans le temps d’horloge (secondes). Certains du délai est attribué à la logique de traitement inhérente. Par conséquent, la surveillance de la tendance croissante est beaucoup plus important que la surveillance de la valeur absolue. Le délai de l’état stable doit être traité par la conception de votre application, pas par l’analyse ou d’alertes.

En cas d’échec, des journaux d’activité et [les journaux de diagnostic](stream-analytics-job-diagnostic-logs.md) sont le meilleur endroit pour commencer à rechercher des erreurs.

## <a name="build-resilient-and-mission-critical-applications"></a>Build résiliente et les applications critiques

Quelle que soit la garantie de contrat SLA des Analytique Azure Stream et avec prudence vous exécutez votre application de bout en bout, pannes se produisent. Si votre application est critique, vous devez être prêt pour les interruptions afin d’effectuer une récupération normale.

Pour alerter les applications, le plus important est de détecter l’alerte suivante. Vous pouvez choisir de redémarrer le travail à partir du temps lors de la récupération, en ignorant les alertes passées. La sémantique de heure de début de travail est par la première fois de sortie, pas la première fois d’entrée. L’entrée est rembobinée vers l’arrière d’une quantité appropriée de temps afin de garantir que la première sortie à l’heure spécifiée est complète et correcte. Vous ne sont pas obtenir les agrégats partielles et déclenchent des alertes de façon inattendue en conséquence.

Vous pouvez également choisir de démarrer la sortie à partir d’une certaine quantité de temps dans le passé. Event Hubs et de IoT Hub stratégies de rétention contiennent une quantité raisonnable de données pour autoriser le traitement dans le passé. L’inconvénient est la rapidité avec laquelle vous pouvez rattraper le retard à l’heure actuelle et commencer à générer des nouvelles alertes en temps voulu. Données perdent rapidement sa valeur au fil du temps, il est donc important de rattraper rapidement à l’heure actuelle. Il existe deux façons de rattraper rapidement :

- Approvisionner davantage de ressources (SU) lorsque rattrape le retard.
- Redémarrer à partir de l’heure actuelle.

Le redémarrage à partir d’actuel l’heure est simple à réaliser, l’inconvénient de devoir de laisser un écart au cours du traitement. Le redémarrage de cette façon peut être OK pour créer des alertes scénarios, mais peut être problématique pour les scénarios de tableau de bord et un non-starter pour l’archivage et de scénarios d’entreposage de données.

L’approvisionnement des ressources plus peut accélérer le processus, mais l’effet d’avoir une forte hausse taux de traitement est complexe.

- Vérifiez que votre travail est flexible pour un plus grand nombre d’unités de streaming. Toutes les requêtes sont évolutifs. Vous devez vous assurer que votre requête est [parallélisée](stream-analytics-parallelization.md).

- Assurez-vous qu’il y a suffisamment de partitions dans les concentrateurs d’événements en amont ou IoT Hub que vous pouvez ajouter davantage d’unités de débit (tu) à l’échelle le débit d’entrée. N’oubliez pas, jusqu'à chaque TU de Hubs d’événements à une vitesse de sortie de 2 Mo/s.

- Assurez-vous que vous avez configuré suffisamment de ressources dans les récepteurs de sortie (base de données SQL, Cosmos DB), afin qu’ils ne pas limiter la forte hausse de sortie, qui peuvent parfois entraîner le blocage du système.

La chose la plus importante consiste à anticiper la modification du taux de traitement, tester ces scénarios avant la production commencée et être prêt à l’échelle le traitement correctement pendant la durée de récupération de défaillance.

Dans le scénario extrême que les événements entrants sont toutes retardée, [il est possible de tous les événements différés sont supprimés](stream-analytics-time-handling.md) si vous avez appliqué une arrivée tardive à votre travail. La suppression des événements peut sembler être un comportement mystérieux au début ; Toutefois, étant donné que Stream Analytique est un moteur de traitement en temps réel, il attend des événements entrants pour être proche de la durée totale. Il doit supprimer les événements qui violent ces contraintes.

### <a name="backfilling-process"></a>Processus de renvoi

Heureusement, le modèle d’archivage de données précédent peut être utilisé pour traiter ces événements tardifs normalement. L’idée est que le travail d’archivage traite les événements entrants dans l’heure d’arrivée et archive les événements dans le compartiment de bon moment dans les objets Blob Azure ou Azure Data Lake Store avec leur heure de l’événement. Peu importe quel moment un événement arrive, ne sont jamais supprimée. Il est alors toujours placé dans le compartiment de bon moment. Lors de la récupération, il est possible de retraiter les événements archivés et le renvoi les résultats dans le magasin de choix.

![Renvoi d’ASA](media/stream-analytics-solution-patterns/backfill.png)

Le processus de renvoi doit être effectuée avec un système qui a un autre modèle de programmation que Azure Stream Analytique probablement de traitement par lots hors connexion. Cela signifie que vous devez ré-implémenter la logique de traitement entier.

Pour un renvoi, il est toujours important pour au moins temporairement approvisionner que plus de ressources à la sortie récepteurs pour gérer un débit plus élevé que l’état stable des besoins de traitement.

|Scénarios  |Redémarrer à partir de maintenant uniquement  |Redémarrer à partir d’heure du dernier arrêt |Redémarrer à partir de now + renvoi avec des événements archivés|
|---------|---------|---------|---------|
|**Tableaux de bord**   |Crée l’écart    |OK pour une brève interruption de service    |Utilisation de l’interruption de longue durée |
|**Génération d’alertes**   |Acceptable |OK pour une brève interruption de service    |N’est pas nécessaire |
|**Application d’approvisionnement en événements** |Acceptable |OK pour une brève interruption de service    |Utilisation de l’interruption de longue durée |
|**Entrepôt de données**   |Perte de données  |Acceptable |N’est pas nécessaire |
|**Analytique en mode hors connexion**  |Perte de données  |Acceptable |N’est pas nécessaire|

## <a name="putting-it-all-together"></a>Exemple complet

Il n’est pas difficile d’imaginer que tous les modèles de solution mentionnées ci-dessus peuvent être combinées dans un système complexe de bout en bout. Le système combiné peut inclure des tableaux de bord, alertes, application d’approvisionnement en événements, d’entreposage de données et fonctionnalités d’analytique en mode hors connexion.

La clé consiste à concevoir votre système dans les modèles composables, pour chaque sous-système peut être généré, testés, mis à niveau et récupérer de manière indépendante.

## <a name="next-steps"></a>Étapes suivantes

Vous avez maintenant vu une variété de modèles de solution à l’aide d’Azure Stream Analytique. Pour approfondir ces connaissances et créer votre premier travail Stream Analytics, consultez les articles suivants :

* [Créer un travail Stream Analytics à l’aide du portail Azure](stream-analytics-quick-create-portal.md).
* [Créer un travail Stream Analytics à l’aide d’Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Créer un travail Stream Analytics à l’aide de Visual Studio](stream-analytics-quick-create-vs.md).
