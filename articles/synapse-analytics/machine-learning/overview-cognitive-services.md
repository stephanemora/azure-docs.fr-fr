---
title: Cognitive Services dans Azure Synapse Analytics
description: Enrichissez vos données avec l’intelligence artificielle (IA) dans Azure Synapse Analytics à l’aide de modèles préformés à partir d’Azure Cognitive Services.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye, negust, ruxu
ms.date: 06/30/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: 3af2efda3d54f8db29d4817357ea8cb826b7c608
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294143"
---
# <a name="cognitive-services-in-azure-synapse-analytics"></a>Cognitive Services dans Azure Synapse Analytics

À l’aide des modèles préformés d’Azure Cognitive Services, vous pouvez enrichir vos données avec l’intelligence artificielle (IA) dans Azure Synapse Analytics.

[Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) regroupent des services cloud qui ajoutent des informations cognitives à vos données, même si vous n’avez pas de compétences en matière de science des données ou d’intelligence artificielle. Vous pouvez utiliser ces services avec vos données dans Synapse Analytics de plusieurs façons :

- L’Assistant Cognitive Services de Synapse Analytics génère du code PySpark dans un notebook Synapse qui se connecte à un cognitive service à l’aide des données d’une table Spark. Ensuite, à l’aide de modèles de Machine Learning préformés, le service vous permet d’ajouter l’intelligence artificielle à vos données.

- Le tutoriel [Créer des applications Machine Learning avec Microsoft Machine Learning pour Apache Spark (préversion)](tutorial-build-applications-use-mmlspark.md) montre comment appeler un certain nombre de cognitive services à l’aide de Microsoft Machine Learning pour Apache Spark (MMLSpark).

- À partir du code PySpark généré par l’Assistant, ou de l’exemple de code MMLSpark fourni dans le tutoriel, vous pouvez écrire votre propre code pour utiliser d’autres cognitive services avec vos données. Pour plus d’informations sur les services disponibles, consultez la [Présentation d’Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md).

## <a name="get-started"></a>Bien démarrer

Le tutoriel [Prérequis de l’utilisation de Cognitive Services dans Azure Synapse](tutorial-configure-cognitive-services-synapse.md) vous guide tout au long de la procédure à suivre avant d’utiliser Cognitive Services dans Synapse Analytics.

## <a name="tutorials"></a>Tutoriels

Les tutoriels suivants fournissent des exemples complets d’utilisation de Cognitive Services dans Synapse Analytics.

- [Analyse des sentiments avec Cognitive Services](tutorial-cognitive-services-sentiment.md) : à l’aide d’un exemple de jeu de données de commentaires de clients, vous générez une table Spark dotée d’une colonne indique le sentiment des commentaires dans chaque ligne.

- [Détection des anomalies avec Cognitive Services](tutorial-cognitive-services-anomaly.md) : à l’aide d’un exemple de jeu de données de série chronologique, vous générez une table Spark dotée d’une colonne indiquant si les données de chaque ligne constituent une anomalie.

- [Créer des applications Machine Learning avec Microsoft Machine Learning pour Apache Spark (préversion)](tutorial-build-applications-use-mmlspark.md) : ce tutoriel montre comment utiliser MMLSpark pour accéder à plusieurs modèles à partir de Cognitive Services.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctionnalités de Machine Learning dans Azure Synapse Analytics](what-is-machine-learning.md)
- [Présentation de Cognitive Services](../../cognitive-services/what-are-cognitive-services.md)
- [Utiliser un notebook échantillon de la galerie Synapse Analytics](quickstart-gallery-sample-notebook.md)