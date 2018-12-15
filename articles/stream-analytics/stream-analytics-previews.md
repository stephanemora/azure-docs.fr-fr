---
title: Fonctionnalités d’évaluation Azure Stream Analytics
description: Cet article répertorie les fonctionnalités d’Azure Stream Analytics qui sont actuellement en cours d’évaluation.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 637afab45e04c68777f8d1b42817c912cdc09941
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133738"
---
# <a name="azure-stream-analytics-preview-features"></a>Fonctionnalités d’évaluation Azure Stream Analytics

Cet article résume toutes les fonctionnalités actuellement en préversion pour Azure Stream Analytics. L’utilisation de fonctionnalités en préversion dans un environnement de production n’est pas recommandée.

## <a name="public-previews"></a>Préversions publiques

Les fonctionnalités suivantes sont disponibles en préversion publique. Vous pouvez tirer parti de ces fonctionnalités aujourd’hui, mais ne les utilisez pas dans votre environnement de production.

### <a name="integration-with-azure-machine-learning"></a>Intégration à Azure Machine Learning

Vous pouvez mettre à l’échelle les tâches Stream Analytics avec des fonctions Machine Learning (ML). Pour en savoir plus sur la façon dont vous pouvez utiliser les fonctions ML dans votre tâche Stream Analytics, consultez [Mettre à l’échelle votre travail Stream Analytics avec des fonctions Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Découvrez un scénario concret avec [Analyse des sentiments à l’aide d’Azure Stream Analytics et Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="session-windows"></a>Fenêtres de session

Stream Analytics a une prise en charge native des fonctions de fenêtrage, permettant aux développeurs de créer des travaux de traitement de flux complexes avec un minimum d’effort. Les [fenêtres de session](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) regroupent les événements qui arrivent au même moment, en filtrant les périodes pendant lesquelles il n’existe aucune donnée. Pour en savoir plus sur les fonctions de fenêtrage, consultez [Présentation des fonctions de fenêtrage de Stream Analytics](stream-analytics-window-functions.md).

### <a name="blob-output-partitioning-by-custom-time"></a>Partitionnement de la sortie des objets blob par heure personnalisée

Azure Stream Analytics peut définir une sortie vers le stockage d’objets Blob en fonction d’attributs de temps personnalisé. Pour plus d’informations, consultez [Modèles de chemin DateTime personnalisé pour les sorties du stockage Blob d’Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md).

### <a name="javascript-user-defined-aggregate"></a>Agrégat JavaScript défini par l’utilisateur

Azure Stream Analytics prend en charge les agrégats définis par l’utilisateur (UDA) et écrits en JavaScript ; ils vous permettent d’implémenter une logique métier avec état complexe. Découvrez comment utiliser les UDA dans la documentation [Agrégats Javascript définis par l’utilisateur Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md). 

### <a name="live-data-testing-in-visual-studio"></a>Tests de données actives dans Visual Studio

Visual Studio Tools pour Azure Stream Analytics améliore la fonctionnalité de test en local qui vous permet de tester vos requêtes sur les flux d’événements en direct, à partir de sources cloud, telles que Event Hub ou IoT hub. Découvrez comment [Tester les données actives localement à l’aide d’Azure Stream Analytics Tools pour Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Fonctions .NET définies par l’utilisateur sur IoT Edge

Avec les fonctions .NET standard définies par l’utilisateur, vous pouvez exécuter le code .NET Standard au sein même de votre pipeline de streaming. Vous pouvez créer des classes C# simples, ou importer des bibliothèques et un projet complet. Une expérience de création et de débogage complète est prise en charge dans Visual Studio. Pour plus d’informations, consultez [Développer des fonctions .NET Standard définies par l’utilisateur pour des tâches Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Préversions privées

Les fonctionnalités suivantes sont disponibles en préversion privée. Pour accéder à ces fonctionnalités en préversion, consultez la page d’[inscription](https://aka.ms/ASApreview1) à la préversion privée d’Azure Stream Analytics.

### <a name="anomaly-detection"></a>Détection des anomalies

Azure Stream Analytics introduit de nouveaux modèles Machine Learning avec la prise en charge de la détection de *pics* et de *chutes* qui vient s’ajouter à la détection des tendances négatives lentes, positives lentes et bidirectionnelles.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Désérialiseur personnalisé C# pour Azure Stream Analytics sur IoT Edge

Les développeurs peuvent désormais implémenter des désérialiseurs personnalisés en C# pour désérialiser les événements reçus par Azure Stream Analytics. Parquet, Protobuf, XML ou tout format binaire sont autant d’exemples de formats pouvant être désérialisés.

### <a name="blob-output-partitioning-by-custom-attribute"></a>Partitionnement de la sortie d’objets Blob par attribut personnalisé

Il est à présent possible de partitionner votre sortie Azure Stream Analytics sur le stockage d’objets Blob en fonction d’une colonne dans votre requête.

### <a name="managed-identities-for-azure-resources-authentication-to-azure-data-lake-storage"></a>Identités managées pour l’authentification des ressources Azure sur Azure Data Lake Storage

Vous pouvez à présent faire fonctionner vos pipelines en temps réel, avec des identités managées pour l’authentification basée sur les ressources Azure lors de l’écriture dans Azure Data Lake Storage Gen1, ce qui vous permet de créer des tâches par programmation. Pour de plus amples renseignements, consultez [Utiliser des identités managées pour les ressources Azure afin d’authentifier les tâches Azure Stream Analytics sur la sortie Azure Data Lake Storage Gen1](stream-analytics-managed-identities-adls.md).

## <a name="next-steps"></a>Étapes suivantes

* [Huit nouvelles fonctionnalités dans Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [Quatre nouvelles fonctionnalités sont désormais disponibles dans Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
