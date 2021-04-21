---
title: Qu’est-ce que l’API Détecteur d’anomalies (multivarié) ?
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble des nouvelles API multivariées en préversion publique de Détecteur d’anomalies.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: détection d’anomalie, Machine Learning, algorithmes
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318807"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>Détection d’anomalie dans les séries chronologiques multivariées (préversion publique)

La première version du service Détecteur d’anomalies d’Azure Cognitive Services vous permettait de créer des solutions de supervision des métriques à l’aide des [API Détecteur d’anomalies de série chronologique univariée](overview.md) faciles à utiliser. En permettant d’analyser les séries chronologiques individuellement, Détecteur d’anomalies (univarié) offre simplicité et scalabilité.

Les nouvelles **API de détection d’anomalie multivariée** permettent aux développeurs d’intégrer facilement l’intelligence artificielle avancée pour détecter les anomalies à partir de groupes de métriques, sans avoir besoin de connaissances en apprentissage automatique ou de données étiquetées. Les dépendances et les intercorrélations entre un maximum de 300 signaux différents sont désormais automatiquement comptabilisées comme des facteurs clés. Cette nouvelle capacité vous aide à protéger de manière proactive vos systèmes complexes tels que les applications logicielles, les serveurs, les machines d’usine, les engins spatiaux, voire votre entreprise, contre les défaillances.

Imaginez 20 capteurs d’un moteur automobile générant 20 signaux différents comme les vibrations, la température, la pression du carburant, etc. Les lectures de ces signaux, prises individuellement, peuvent ne pas vous renseigner sur les problèmes au niveau du système, mais, ensemble, elles peuvent représenter la santé du moteur. Lorsque l’interaction de ces signaux s’écarte de la plage habituelle, la fonctionnalité de détection d’anomalie multivariée peut détecter l’anomalie comme un expert chevronné. Les modèles d’IA sous-jacents sont formés et personnalisés à l’aide de vos données, de sorte qu’ils comprennent les besoins uniques de votre entreprise. Grâce aux nouvelles API de Détecteur d’anomalies, les développeurs peuvent désormais intégrer facilement les capacités de détection d’anomalie de séries chronologiques multivariées dans les solutions de maintenance prédictive, les solutions de supervision AIOps pour les logiciels d’entreprise complexes ou les outils décisionnels.

## <a name="when-to-use-multivariate-versus-univariate"></a>Quand utiliser des données **multivariées** plutôt qu’**univariées** ?

Utilisez les API de détection d’anomalie univariée si votre objectif est de détecter les anomalies par rapport à un modèle normal sur chaque série chronologique uniquement sur la base de leurs propres données historiques. Exemples : Vous souhaitez détecter les anomalies du chiffre d’affaires quotidien sur la base des données de chiffre d’affaires elles-mêmes, ou vous souhaitez détecter un pic d’utilisation de l’UC uniquement sur la base des données de l’UC.
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![Graphique en courbes de série chronologique avec les valeurs fluctuantes d’une seule variable capturées par une ligne bleue et les anomalies identifiées par des cercles orange.](./media/anomaly_detection2.png)

Utilisez les API de détection d’anomalie multivariée ci-dessous si votre objectif est de détecter les anomalies au niveau du système à partir d’un groupe de données de série chronologique. En particulier, lorsqu’une série chronologique donnée ne vous dit pas grand-chose et que vous devez examiner tous les signaux (un groupe de séries chronologiques) de manière holistique pour déterminer un problème au niveau du système. Exemple : Vous avez une ressource physique coûteuse comme un avion, un équipement sur une plateforme pétrolière ou un satellite. Chacun de ces biens possède des dizaines ou des centaines de types de capteurs différents. Vous devez examiner tous les signaux de série chronologique de ces capteurs pour déterminer s’il existe un problème au niveau du système.

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![Graphiques en courbes de plusieurs séries chronologiques pour les variables suivantes : vibration, température, pression, vélocité, vitesse de rotation, avec les anomalies mises en évidence en orange.](./media/multivariate-graph.png)

## <a name="region-support"></a>Prise en charge de la région

La préversion publique de Détecteur d’anomalies (multivarié) est actuellement disponible dans trois régions : USA Ouest 2, USA Est 2 et Europe Ouest.

## <a name="algorithms"></a>Algorithmes

- [Détection d’anomalie de séries chronologiques multivariées via un réseau à graphe d’attention](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>Rejoindre la communauté du détecteur d’anomalies

- Rejoindre le [groupe Anomaly Detector Advisors sur Microsoft Teams](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>Étapes suivantes

- [Démarrages rapides](./quickstarts/client-libraries-multivariate.md).
- [Meilleures pratiques](./concepts/best-practices-multivariate.md) : cet article porte sur les modèles recommandés à utiliser avec les API multivariées.
