---
title: Qu’est-ce que le service Azure Metrics Advisor ?
titleSuffix: Azure Applied AI Services
description: Qu’est-ce que Metrics Advisor ?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 07/06/2021
ms.author: mbullwin
ms.openlocfilehash: 295c166ec80eba638798dd452e27de945e41e9a6
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341856"
---
# <a name="what-is-azure-metrics-advisor"></a>Qu’est-ce qu’Azure Metrics Advisor ? 

Metrics Advisor fait partie [Azure Applied AI Services](../../applied-ai-services/what-are-applied-ai-services.md), qui utilise l’intelligence artificielle pour superviser les données et détecter les anomalies dans des données de série chronologique. Le service automatise le processus d’application de modèles à vos données et fournit un ensemble d’API et un espace de travail web pour l’ingestion des données, la détection des anomalies et les diagnostics, sans avoir besoin de connaître le Machine Learning. Les développeurs peuvent créer des applications AIOps, de maintenance prédictive et de supervision métier sur le service. Utilisez Metrics Advisor pour :

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

## <a name="real-time-notification-through-multiple-channels"></a>Notifications en temps réel par le biais de plusieurs canaux

Chaque fois que des anomalies sont détectées, Metrics Advisor peut [envoyer des notifications en temps réel](how-tos/alerts.md) par le biais de plusieurs canaux à l’aide de hooks, tels que des hooks d’e-mail, des webhooks, des hooks Teams et des hooks Azure DevOps. La configuration d’alerte flexible vous permet de personnaliser le moment et l’emplacement d’envoi d’une notification.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Insights de diagnostic intelligent via l’analyse des anomalies

### <a name="analyze-root-cause-into-specific-dimension"></a>Analyser la cause racine dans une dimension spécifique 

Metrics Advisor combine les anomalies détectées sur la même métrique multidimensionnelle dans une arborescence de diagnostics pour vous aider à analyser la cause racine dans une dimension spécifique. Des insights automatisés sont également disponibles en analysant la contribution la plus élevée de chaque dimension. 

### <a name="cross-metrics-analysis-using-metrics-graph"></a>Analyse des mesures croisées à l’aide du graphique de métriques

Un [graphique de métriques](./how-tos/metrics-graph.md) indique la relation entre les métriques. L’analyse des mesures croisées peut être activée pour vous aider à détecter un état anormal parmi toutes les mesures associées dans une vue holistique. Et finissent par localiser la dernière cause racine.

Pour plus d’informations, consultez [Comment diagnostiquer un incident](./how-tos/diagnose-an-incident.md).

## <a name="typical-workflow"></a>Flux de travail classique

Le flux de travail est simple : après l’intégration de vos données, vous pouvez ajuster la détection des anomalies et créer des configurations adaptées à votre scénario.

1. [Créez une ressource Azure](https://go.microsoft.com/fwlink/?linkid=2142156) pour Metrics Advisor. 
2. Générez votre premier moniteur à l’aide du portail web.
    1. [Intégrer vos données](./how-tos/onboard-your-data.md)
    2. [Ajuster la configuration de la détection des anomalies](./how-tos/configure-metrics.md)
    3. [S’abonner aux notifications d’anomalies](./how-tos/alerts.md)
    4. [Afficher les insights de diagnostic](./how-tos/diagnose-an-incident.md)
3. Utilisez l’API REST pour personnaliser votre instance.

## <a name="video"></a>Vidéo
* [Présentation de Metrics Advisor](https://www.youtube.com/watch?v=0Y26cJqZMIM)
* [Nouveautés de Cognitive Services](https://www.youtube.com/watch?v=7tCLJHdBZgM)

## <a name="data-retention--limitation"></a>Conservation et limitation des données : 

Metrics Advisor conservera au maximum **10 000** intervalles de temps ([qu’est-ce qu’un intervalle ?](tutorials/write-a-valid-query.md#what-is-an-interval)) en comptant à partir du timestamp actuel, qu’il y ait des données disponibles ou non. Les données qui tombent en dehors de la fenêtre seront supprimées.  Mappage de la conservation des données en nombre de jours pour différentes granularités de métriques : 

| Granularité(min) |    Rétention(jour) |
|------------------| ------------------|
|  1 | 6,94 |
|  5 | 34,72|
| 15 | 104,1|
| 60(=horaire) | 416,67 |
| 1 440(=quotidienne)|10 000|

Il existe également d'autres limitations, veuillez vous référer à la [FAQ](faq.yml#what-are-the-data-retention-and-limitations-of-metrics-advisor-) pour plus de détails. 

## <a name="next-steps"></a>Étapes suivantes

* Explorez un guide de démarrage rapide : [Surveillez votre première métrique sur le web](quickstarts/web-portal.md).
* Explorez un guide de démarrage rapide : [Utilisez les API REST pour personnaliser votre solution](./quickstarts/rest-api-and-client-library.md).