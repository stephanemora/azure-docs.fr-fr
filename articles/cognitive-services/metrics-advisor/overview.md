---
title: Qu’est-ce que le service Metrics Advisor ?
titleSuffix: Azure Cognitive Services
description: Qu’est-ce que Metrics Advisor ?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: dfdd7286013bbb6462fb8e5b1bdf52e6ed738029
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384678"
---
# <a name="what-is-metrics-advisor-preview"></a>Qu’est-ce que Metrics Advisor (préversion) ? 

Metrics Advisor fait partie d’Azure Cognitive Services, qui utilise l’intelligence artificielle pour superviser les données et détecter les anomalies dans des données de série chronologique. Le service automatise le processus d’application de modèles à vos données et fournit un ensemble d’API et un espace de travail web pour l’ingestion des données, la détection des anomalies et les diagnostics, sans avoir besoin de connaître le Machine Learning. Les développeurs peuvent créer des applications AIOps, de maintenance prédictive et de supervision métier sur le service. Utilisez Metrics Advisor pour :

* Analyser des données multidimensionnelles à partir de plusieurs sources de données
* Identifier et corréler les anomalies
* Configurer et ajuster le modèle de détection d’anomalies utilisé sur vos données
* Diagnostiquer les anomalies et aider à analyser la cause racine

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Vue d’ensemble de Metrics Advisor":::

Cette documentation contient les types d’articles suivants :
* Les [guides de démarrage rapide](./Quickstarts/web-portal.md) sont des instructions pas à pas qui vous permettent d’effectuer des appels au service et d’obtenir des résultats en peu de temps. 
* Les [guides patiques](./how-tos/onboard-your-data.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.
* Les [articles conceptuels](glossary.md) fournissent des explications approfondies sur les fonctions et fonctionnalités du service.

## <a name="connect-to-a-variety-of-data-sources"></a>Se connecter à diverses sources de données

Metrics Advisor peut se connecter à de nombreux magasins de données et [ingérer des données de métriques multidimensionnelles](how-tos/onboard-your-data.md), notamment : SQL Server, stockage Blob Azure, MongoDB, etc.

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Détection d’anomalies facile à utiliser et personnalisable

* Metrics Advisor sélectionne automatiquement le meilleur modèle pour vos données, sans avoir à connaître le machine learning.
* Surveillez automatiquement chaque série chronologique au sein de [métriques multidimensionnelles](glossary.md#multi-dimensional-metric).
* Utilisez l’[ajustement de paramètres](how-tos/configure-metrics.md) et les [commentaires interactifs](how-tos/anomaly-feedback.md) pour personnaliser le modèle appliqué à vos données et les futurs résultats de détection d’anomalies.

## <a name="real-time-alerts-through-multiple-channels"></a>Alertes en temps réel par le biais de plusieurs canaux

Chaque fois que des anomalies sont détectées, Metrics Advisor peut [envoyer des alertes en temps réel](how-tos/alerts.md) par le biais de plusieurs canaux à l’aide de hooks, tels que des hooks d’e-mail, des webhooks et des hooks Azure DevOps. Les règles d’alerte flexibles vous permettent de personnaliser les alertes qui sont envoyées et leur destination.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Insights de diagnostic intelligent via l’analyse des anomalies

Analysez les anomalies détectées sur les métriques multidimensionnelles et générez des [insights de diagnostic intelligent](how-tos/diagnose-incident.md) comprenant la cause racine la plus probable, les arborescences de diagnostic, l’exploration des métriques, etc. En configurant un [graphe de métriques](how-tos/metrics-graph.md), une analyse croisée des métriques peut être activée pour vous aider à visualiser les incidents.


## <a name="typical-workflow"></a>Flux de travail classique

Le flux de travail est simple : après l’intégration de vos données, vous pouvez ajuster la détection des anomalies et créer des configurations adaptées à votre scénario.

1. [Créez une ressource Azure](https://go.microsoft.com/fwlink/?linkid=2142156) pour Metrics Advisor. 
2. Générez votre premier moniteur à l’aide du portail web.
    1. Intégrer vos données
    2. Affiner la détection des anomalies
    3. S’abonner aux alertes
    4. Afficher les insights de diagnostic
3. Utilisez l’API REST pour personnaliser votre instance.

## <a name="next-steps"></a>Étapes suivantes

* Explorez un guide de démarrage rapide : [Surveillez votre première métrique sur le web](quickstarts/web-portal.md).
* Explorez un guide de démarrage rapide : [Utilisez les API REST pour personnaliser votre solution](./quickstarts/rest-api-and-client-library.md).
