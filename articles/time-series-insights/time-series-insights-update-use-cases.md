---
title: Cas d’usage de la préversion d’Azure Time Series Insights | Microsoft Docs
description: Comprendre les cas d’usage de la préversion d’Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 787445d5186a173b2cba674b36cd95879cc863e5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389996"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Cas d’usage de la préversion d’Azure Time Series Insights

Cet article résume plusieurs cas d’utilisation courants pour la version préliminaire d’Azure temps série Insights. Les recommandations de cet article constituent comme point de départ pour développer vos applications et solutions avec Time Series Insights.

Plus précisément, cet article répond aux questions suivantes :

* Quels sont les cas d’usage courants de Time Series Insights ?
* Quels sont les avantages de l’utilisation de Time Series Insights pour [exploration de données et de détection d’anomalie visual](#data-exploration-and-visual-anomaly-detection)?
* Quels sont les avantages de l’utilisation de Time Series Insights pour [analyse opérationnelle et l’efficacité des processus](#operational-analysis-and-driving-process-efficiency)?
* Quels sont les avantages de l’utilisation de Time Series Insights pour [avancées d’analytique](#advanced-analytics)?

Une vue d’ensemble de ces scénarios d’utilisation est décrite dans les sections suivantes.

## <a name="introduction"></a>Présentation

Azure Time Series Insights est une offre platform-as-a-service de bout en bout. Elle est utilisée pour collecter, traiter, stocker, analyser et interroger des données à l’échelle IoT optimisées pour une série chronologique hautement contextualisée. Time Series Insights est idéal pour l’exploration de données ad hoc et l’analyse opérationnelle. Time Series Insights est un service extensible de manière unique, personnalisé offre que répond au large a besoin des déploiements IoT industriels.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Exploration des données et détection d’anomalies visuelles

Explorez et analysez instantanément des milliards d’événements pour repérer les anomalies et découvrir les tendances cachées de vos données. Time Series Insights offre des performances en temps quasi réel pour vos charges de travail d’analyse IoT et DevOps.

[![Explorateur de données](media/v2-update-use-cases/data-explorer.svg)](media/v2-update-use-cases/data-explorer.svg#lightbox)

La plupart des clients estiment que la durée d’analyse constitue l’un des principaux points forts de Time Series Insights. Time Series Insights ne requiert aucune préparation de données initiale. Très rapide, il vous connecte à des milliards d’événements dans votre environnement Azure IoT Hub ou Azure Event Hubs en quelques minutes. Une fois connecté, vous pouvez visualiser et analyser des milliards d’événements pour repérer les anomalies et découvrir les tendances cachées de vos données.

Time Series Insights est intuitif et simple à utiliser. Vous pouvez interagir avec vos données sans avoir à écrire une seule ligne de code. Inutile d’apprendre un nouveau langage. Time Series Insights propose un système granulaire d’exécution de requêtes textuelles pour les utilisateurs expérimentés qui maîtrisent SQL. Quant aux novices, ils peuvent utiliser la plateforme via un mode d’exploration simplifié.

Les clients tirent parti de la vitesse de diagnostic des problèmes liés aux ressources. Ils peuvent effectuer des opérations de développement pour identifier la cause racine d’un bogue dans une solution IoT. Ils peuvent également identifier les zones à examiner dans le cadre d’initiatives de science des données.  

Il existe trois méthodes principales pour interagir avec les données stockées dans Time Series Insights :

- La méthode la plus simple pour commencer consiste à utiliser l’explorateur de la préversion de Time Series Insights. Vous pouvez l’utiliser pour visualiser rapidement toutes vos données IoT au même endroit. Il fournit des outils tels qu’une carte thermique pour vous aider à détecter les anomalies dans vos données. Il propose également une vue en perspective. Vous pouvez l’utiliser pour comparer jusqu’à quatre vues provenant d’un ou plusieurs environnements Time Series Insights dans un tableau de bord unique. Le tableau de bord vous donne une vue d’ensemble des données de série chronologique de tous vos emplacements. En savoir plus sur [l’explorateur de la préversion de Time Series Insights](./time-series-insights-update-explorer.md). Pour planifier votre environnement Time Series Insights, consultez [Time Series Insights planning](./time-series-insights-update-plan.md) (Planification de Time Series Insights).

- La deuxième méthode consiste à utiliser le kit de développement logiciel (SDK) JavaScript pour incorporer rapidement de puissants graphiques dans votre application web. Avec seulement quelques lignes de code, vous pouvez créer des requêtes puissantes. Utilisez-les pour remplir les graphiques en courbes, les graphiques à secteurs, les graphiques à barres, les cartes thermiques, les grilles de données et bien plus encore. Tous ces éléments sont prêts à l’emploi dans le kit de développement logiciel (SDK). Le kit de développement logiciel (SDK) extrait également les API de requête Time Series Insights. Vous pouvez les utiliser pour créer des prédicats de type SQL afin d’interroger les données que vous souhaitez afficher dans un tableau de bord. Pour les solutions hybrides de couche présentation, Time Series Insights propose des URL paramétrables. Elles fournissent des points de connexion transparents avec l’explorateur de la préversion de Time Series Insights pour des analyses détaillées des données.

    * Lire le [bibliothèque cliente de temps série Insights JS](tutorial-explore-js-client-lib.md) et le [client de Time Series Insights](https://github.com/Microsoft/tsiclient) documentation pour en savoir plus sur le SDK JavaScript.

    * En savoir plus sur le partage des URL et la nouvelle interface utilisateur en examinant [visualiser les données dans l’Explorateur Azure temps série Insights Preview](time-series-insights-update-explorer.md).

- La troisième méthode consiste à utiliser les puissantes API pour interroger les données stockées dans Time Series Insights. Time Series Insights a tels que les opérateurs temporels `from`, `to`, `first`, et `last`. Il possède des transformations et des agrégations telles que `average`, `min`, `max`, `split by`, `order by`, et `DateHistogram`. Il a également comme opérateurs de filtrage `has`, `in`, `and`, `or`, `greater than`, et `REGEX`. Tous ces opérateurs permettent aux applications en aval d’identifier rapidement des tendances et des modèles intéressants dans vos données. Utilisez-les pour remplir les visualisations personnalisées afin de détecter les anomalies.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analyse opérationnelle et efficacité du processus de pilotage

Utilisez Time Series Insights pour surveiller l’intégrité, l’utilisation et les performances des équipements à grande échelle. Time Series Insights permet de mesurer facilement l’efficacité opérationnelle. Il permet de gérer des charges de travail IoT diverses et imprévisibles sans nuire aux performances d’ingestion ou de requête.

[![Vue d’ensemble](media/v2-update-use-cases/overview.svg)](media/v2-update-use-cases/overview.svg#lightbox)

La diffusion et le traitement en continu de données provenant des processus opérationnels peuvent être avantageux pour n’importe quelle activité s’ils sont associés à la technologie ou à la solution appropriée. Souvent, ces solutions sont une combinaison de plusieurs systèmes. Elles permettent l’exploration et l’analyse des données qui évoluent en permanence, en particulier en ce qui concerne l’IoT, et partagent un modèle commun.

Ces modèles reposent souvent sur des plateformes compatibles avec l’IoT qui ingèrent des milliards d’événements provenant d’appareils et de capteurs qui regroupent différents paramètres régionaux. Ces systèmes traitent et analysent les données en continu pour générer des informations et des actions pertinentes en temps réel. Les données sont généralement archivées en stockage chaud ou froid pour des analyses par lots en temps quasi réel.

Les données collectées sont soumises à une série de traitements visant à les nettoyer et à les contextualiser dans le cadre de scénarios de requêtes et d’analyses en aval. Azure propose des services enrichis qui peuvent être appliqués à des scénarios IoT tels que la maintenance et la création de ressources. Ces services incluent Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning et Power BI.

L’architecture de la solution peut être obtenue de la manière suivante :

- Ingérez des données via IoT Hub ou Event Hubs pour une sécurité, un débit et une latence de qualité optimale.
- Effectuez le traitement des données et les calculs. Synthétisez les données ingérées via des services tels que Stream Analytics, Logic Apps et Azure Functions. Le service que vous utilisez varie selon les besoins spécifiques en matière de traitement de données.
- Les signaux calculés à partir du pipeline de traitement sont envoyés à Time Series Insights pour stockage et analyse.

Time Series Insights permet d’explorer les données en temps quasi réel et fournit des analyses basées sur les ressources par rapport aux données historiques. Selon les besoins de votre entreprise, les travaux MapReduce et Hive peuvent être exécutés sur les données stockées dans Time Series Insights en connectant Time Series Insights à Azure HDInsight. Les données stockées dans Time Series Insights sont disponibles pour Power BI et d’autres applications client par le biais des API de requête de surface publique Time Series Insights. Ces données peuvent être utilisées pour les scénarios d’activité détaillée et d’intelligence opérationnelle.

## <a name="advanced-analytics"></a>Analytique avancée

Bénéficiez d’une intégration avec des services analytiques avancés tels que Machine Learning et Azure Databricks. Time Series Insights saisit des données brutes provenant de millions d’appareils. Il ajoute des données contextuelles qui peuvent être utilisées en toute transparence par une suite de services Azure Analytics.

[![Analytics](media/v2-update-use-cases/advanced-analytics.svg)](media/v2-update-use-cases/advanced-analytics.svg#lightbox)

Les analyses avancées et l’apprentissage machine consomment et traitent de grands volumes de données. Ces données sont utilisées pour prendre des décisions reposant sur les données et pour réaliser des analyses prédictives. Dans le cadre de l’IoT, les algorithmes d’analyse avancée s’enrichissent à partir de données collectées via des millions d’appareils. Ces appareils transmettent des données plusieurs fois par seconde. Les données collectées à partir d’appareils IoT sont brutes. Elles ne disposent pas d’informations contextuelles telles que l’emplacement de l’appareil et l’unité de lecture du capteur. Par conséquent, ces données brutes sont difficiles à exploiter directement dans le cadre d’analyses avancées.

Pour combler les lacunes des données IoT pour effectuer des analyses avancées, Time Series Insights a recours à deux méthodes simples et économiques :

- Tout d’abord, Time Series Insights collecte les données de télémétrie brutes provenant de millions d’appareils à l’aide de IoT Hub. Il enrichit ces données avec des informations contextuelles, puis les transforme en format PARQUET. Ce format peut facilement être intégré à d’autres services d’analyse avancée, tels que Machine Learning, Azure Databricks et d’autres applications tierces.

    Time Series Insights peut servir de source fiable pour toutes les données au sein d’une organisation. Il crée un référentiel central pour les charges de travail en aval à utiliser. Étant donné que Time Series Insights est un service de stockage en temps quasi réel, les modèles d’analyse avancée peuvent s’enrichir en permanence à partir des données de télémétrie IoT entrantes. Par conséquent, les modèles peuvent effectuer des prévisions plus précises.

- En outre, Time Series Insights peut être enrichi à l’aide des sorties des modèles Machine Learning et de prédiction pour visualiser et stocker leurs résultats. Cette procédure permet aux organisations d’optimiser et d’ajuster leurs modèles. Time Series Insights vous permet de facilement visualiser les données de télémétrie sur le même plan que les résultats du modèle qui a bénéficié de l’apprentissage. De cette façon, il permet aux équipes de science des données d’identifier les anomalies et les modèles.  

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’explorateur de la préversion de Time Series Insights](./time-series-insights-update-explorer.md).
- Lecture [temps série Insights Preview planification](./time-series-insights-update-plan.md) de planifier votre environnement.
- Consultez la documentation [client de Time Series Insights](https://github.com/Microsoft/tsiclient).
