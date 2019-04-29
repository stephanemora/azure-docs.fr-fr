---
title: Fonctionnalités d’évaluation Azure Stream Analytics
description: Cet article répertorie les fonctionnalités d’Azure Stream Analytics qui sont actuellement en cours d’évaluation.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.openlocfilehash: 08430f3eee858cdb6c9a7fbdfe11bd4c00ef148d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485673"
---
# <a name="azure-stream-analytics-preview-features"></a>Fonctionnalités d’évaluation Azure Stream Analytics

Cet article résume toutes les fonctionnalités actuellement en préversion pour Azure Stream Analytics. L’utilisation de fonctionnalités en préversion dans un environnement de production n’est pas recommandée.

## <a name="public-previews"></a>Préversions publiques

Les fonctionnalités suivantes sont disponibles en préversion publique. Vous pouvez tirer parti de ces fonctionnalités aujourd’hui, mais ne les utilisez pas dans votre environnement de production.

### <a name="anomaly-detection"></a>Détection des anomalies

Azure Stream Analytics introduit de nouveaux modèles Machine Learning avec la prise en charge de la détection de *pics* et de *chutes* qui vient s’ajouter à la détection des tendances négatives lentes, positives lentes et bidirectionnelles. Pour plus d’informations, visitez [détection d’anomalies dans Azure Stream Analytique](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="sql-database-reference-data"></a>Données de référence SQL Database

Azure Stream Analytics prend en charge l'utilisation d'Azure SQL Database comme source d'entrée de données de référence. Vous pouvez utiliser les données SQL Database comme données de référence pour votre travail Stream Analytics sur le portail Azure et dans Visual Studio avec les outils Stream Analytics. Pour plus d'informations, consultez [Utiliser les données de référence d'une instance de SQL Database pour un travail Azure Stream Analytics](sql-reference-data.md).

### <a name="integration-with-azure-machine-learning"></a>Intégration à Azure Machine Learning

Vous pouvez mettre à l’échelle les tâches Stream Analytics avec des fonctions Machine Learning (ML). Pour en savoir plus sur la façon dont vous pouvez utiliser les fonctions ML dans votre tâche Stream Analytics, consultez [Mettre à l’échelle votre travail Stream Analytics avec des fonctions Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Découvrez un scénario concret avec [Analyse des sentiments à l’aide d’Azure Stream Analytics et Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Agrégat JavaScript défini par l’utilisateur

Azure Stream Analytics prend en charge les agrégats définis par l’utilisateur (UDA) et écrits en JavaScript ; ils vous permettent d’implémenter une logique métier avec état complexe. Découvrez comment utiliser les UDA dans la documentation [Agrégats Javascript définis par l’utilisateur Azure Stream Analytics](stream-analytics-javascript-user-defined-aggregates.md). 

### <a name="live-data-testing-in-visual-studio"></a>Tests de données actives dans Visual Studio

Visual Studio Tools pour Azure Stream Analytics améliore la fonctionnalité de test en local qui vous permet de tester vos requêtes sur les flux d’événements en direct, à partir de sources cloud, telles que Event Hub ou IoT hub. Découvrez comment [Tester les données actives localement à l’aide d’Azure Stream Analytics Tools pour Visual Studio](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>Fonctions .NET définies par l’utilisateur sur IoT Edge

Avec les fonctions .NET standard définies par l’utilisateur, vous pouvez exécuter le code .NET Standard au sein même de votre pipeline de streaming. Vous pouvez créer des classes C# simples, ou importer des bibliothèques et un projet complet. Une expérience de création et de débogage complète est prise en charge dans Visual Studio. Pour plus d’informations, consultez [Développer des fonctions .NET Standard définies par l’utilisateur pour des tâches Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="private-previews"></a>Préversions privées

Les fonctionnalités suivantes sont disponibles en préversion privée.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>Désérialiseur personnalisé C# pour Azure Stream Analytics sur IoT Edge

Les développeurs peuvent désormais implémenter des désérialiseurs personnalisés en C# pour désérialiser les événements reçus par Azure Stream Analytics. Parquet, Protobuf, XML ou tout format binaire sont autant d’exemples de formats pouvant être désérialisés.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code pour Azure Stream Analytics

Les travaux Azure Stream Analytics peuvent être créés dans Visual Studio Code. Pour accéder à outils des fonctionnalités en version préliminaire privée, contactez à *ASAToolsfeedback\@microsoft.com*.

## <a name="next-steps"></a>Étapes suivantes

* [Huit nouvelles fonctionnalités dans Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)

* [Quatre nouvelles fonctionnalités sont désormais disponibles dans Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)
