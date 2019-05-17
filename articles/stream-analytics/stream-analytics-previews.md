---
title: Fonctionnalités d’évaluation Azure Stream Analytics
description: Cet article répertorie les fonctionnalités d’Azure Stream Analytics qui sont actuellement en cours d’évaluation.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: f3838bf6b9f7daa24c0cdb5b0c5a08d41d164530
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561133"
---
# <a name="azure-stream-analytics-preview-features"></a>Fonctionnalités d’évaluation Azure Stream Analytics

Cet article résume toutes les fonctionnalités actuellement en préversion pour Azure Stream Analytics. L’utilisation de fonctionnalités en préversion dans un environnement de production n’est pas recommandée.

## <a name="public-previews"></a>Préversions publiques

Les fonctionnalités suivantes sont disponibles en préversion publique. Vous pouvez tirer parti de ces fonctionnalités aujourd’hui, mais ne les utilisez pas dans votre environnement de production.

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Visual Studio Code pour l’Analytique Azure Stream (publiées en mai 2019)

Les travaux Azure Stream Analytics peuvent être créés dans Visual Studio Code. Consultez notre [didacticiel de prise en main de VS Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

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

## <a name="other-previews"></a>Autres versions préliminaires

Les fonctionnalités suivantes sont également disponibles en version préliminaire.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>C#désérialiseur personnalisé pour Azure Stream Analytique sur IoT Edge et le Cloud (Announced mai 2019)

Les développeurs peuvent implémenter un désérialiseur personnalisé dans C# pour désérialiser les événements reçus par Azure Stream Analytique. Parquet, Protobuf, XML ou tout format binaire sont autant d’exemples de formats pouvant être désérialisés. Inscrivez-vous pour cette version préliminaire [ici](https://aka.ms/asapreview1).

### <a name="parquet-output-announced-may-2019"></a>Sortie parquet (annoncée dans mai 2019)
Parquet est un format de colonne l’activation du traitement du big data efficace. En affichant les données au format Parquet dans un lac de données, vous pouvez tirer parti d’Azure Stream Analytique à grande échelle power ETL de diffusion en continu et exécuter le traitement par lots, former des algorithmes d’apprentissage automatique ou exécuter des requêtes interactives sur vos données d’historique. Inscrivez-vous pour cette version préliminaire [ici](https://aka.ms/asapreview1).

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>Intégration d’un seul clic à Event Hubs (Announced mai 2019) 
Avec cette intégration, vous serez maintenant en mesure de visualiser les données entrantes et commencer à écrire une requête Analytique de Stream en un seul clic à partir du portail du Hub d’événements. Une fois que votre requête est prête, vous ne pourrez pas il mettons en production en quelques clics et commencer à obtenir des informations en temps réel. Cela réduit considérablement le temps et les coûts pour développer des solutions d’analytique en temps réel. Inscrivez-vous pour cette version préliminaire [ici](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack-announced-may-2019"></a>Prise en charge pour Azure Stack (annoncée dans mai 2019)
Cette fonctionnalité est activée sur le runtime Azure IoT Edge, tire parti des fonctionnalités d’Azure Stack personnalisées, telles que la prise en charge native pour les entrées locales et génère en cours d’exécution sur Azure Stack (par exemple Event Hubs, IoT Hub, stockage d’objets Blob). Cette nouvelle intégration permet de générer des architectures hybrides qui peuvent analyser vos données à proximité où elle est générée, en réduisant la latence et optimiser des insights.
Inscrivez-vous pour cette version préliminaire [ici](https://aka.ms/asapreview1).

