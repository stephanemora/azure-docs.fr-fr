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
ms.openlocfilehash: e913cc4738b0f58a411e80f3e2b602a072f28665
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112005984"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>Détection d’anomalie dans les séries chronologiques multivariées (préversion publique)

Les nouvelles **API de détection d’anomalie multivariée** permettent aux développeurs d’intégrer facilement l’intelligence artificielle avancée pour détecter les anomalies à partir de groupes de métriques, sans avoir besoin de connaissances en apprentissage automatique ou de données étiquetées. Les dépendances et les intercorrélations entre un maximum de 300 signaux différents sont désormais automatiquement comptabilisées comme des facteurs clés. Cette nouvelle capacité vous aide à protéger de manière proactive vos systèmes complexes tels que les applications logicielles, les serveurs, les machines d’usine, les engins spatiaux, voire votre entreprise, contre les défaillances.

![Graphiques en courbes de plusieurs séries chronologiques pour les variables suivantes : vibration, température, pression, vélocité, vitesse de rotation, avec les anomalies mises en évidence en orange.](./media/multivariate-graph.png)

Imaginez 20 capteurs d’un moteur automobile générant 20 signaux différents comme les vibrations, la température, la pression du carburant, etc. Les lectures de ces signaux, prises individuellement, peuvent ne pas vous renseigner sur les problèmes au niveau du système, mais, ensemble, elles peuvent représenter la santé du moteur. Lorsque l’interaction de ces signaux s’écarte de la plage habituelle, la fonctionnalité de détection d’anomalie multivariée peut détecter l’anomalie comme un expert chevronné. Les modèles d’IA sous-jacents sont formés et personnalisés à l’aide de vos données, de sorte qu’ils comprennent les besoins uniques de votre entreprise. Grâce aux nouvelles API de Détecteur d’anomalies, les développeurs peuvent désormais intégrer facilement les capacités de détection d’anomalie de séries chronologiques multivariées dans les solutions de maintenance prédictive, les solutions de supervision AIOps pour les logiciels d’entreprise complexes ou les outils décisionnels.

## <a name="when-to-use-multivariate-versus-univariate"></a>Quand utiliser des données **multivariées** plutôt qu’**univariées** ?

Si votre objectif est de détecter les anomalies par rapport à un modèle normal sur chaque série chronologique uniquement sur la base de leurs propres données historiques, utilisez les API de détection d’anomalie univariée. Par exemple, vous voulez détecter quotidiennement les anomalies de chiffre d’affaires sur la base des données de chiffre d’affaires elles-mêmes, ou vous voulez détecter un pic d’utilisation du processeur uniquement sur la base des données du processeur.

Si votre objectif est de détecter les anomalies au niveau du système à partir d’un groupe de données de séries chronologiques, utilisez les API de détection d’anomalie multivariée. En particulier, lorsqu’une série chronologique donnée ne vous dit pas grand-chose et que vous devez examiner tous les signaux (un groupe de séries chronologiques) de manière holistique pour déterminer un problème au niveau du système. Par exemple, vous avez une ressource physique coûteuse comme un avion, un équipement sur une plateforme pétrolière ou un satellite. Chacun de ces biens possède des dizaines ou des centaines de types de capteurs différents. Vous devez examiner tous les signaux de série chronologique de ces capteurs pour déterminer s’il existe un problème au niveau du système.

## <a name="notebook"></a>Notebook

Pour savoir comment appeler l’API Détecteur d’anomalies (multivarié), essayez ce [notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb). Ce notebook Jupyter montre comment envoyer une demande d’API et visualiser le résultat.

Pour exécuter le notebook, vous devez obtenir une **clé d’abonnement** d’API Détecteur d’anomalies valide et un **point de terminaison d’API**. Dans le notebook, ajoutez votre clé d’abonnement d’API Détecteur d’anomalies valide à la variable `subscription_key`, et remplacez la variable `endpoint` par votre point de terminaison.


## <a name="region-support"></a>Prise en charge de la région

La préversion publique du détecteur d’anomalies multivarié est actuellement disponible dans six régions : USA Ouest 2, Europe Ouest, USA Est 2, USA Centre Sud, USA Est et Royaume-Uni Sud.

## <a name="algorithms"></a>Algorithmes

Pour plus d’informations sur les algorithmes utilisés, consultez les documents techniques suivants :

* Blog : [Introducing Multivariate Anomaly Detection](https://techcommunity.microsoft.com/t5/azure-ai/introducing-multivariate-anomaly-detection/ba-p/2260679) (Introduction à la détection d’anomalies multivariée)
* Article : [Détection d’anomalies de séries chronologiques multivariée via un graphe de réseau d’attention](https://arxiv.org/abs/2009.02040)


> [!VIDEO https://www.youtube.com/embed/FwuI02edclQ]


## <a name="join-the-anomaly-detector-community"></a>Rejoindre la communauté du détecteur d’anomalies

- Rejoindre le [groupe Anomaly Detector Advisors sur Microsoft Teams](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>Étapes suivantes

- [Démarrages rapides](./quickstarts/client-libraries-multivariate.md).
- [Meilleures pratiques](./concepts/best-practices-multivariate.md) : cet article porte sur les modèles recommandés à utiliser avec les API multivariées.
