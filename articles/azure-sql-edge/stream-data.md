---
title: Streaming de données dans Azure SQL Edge (préversion)
description: En savoir plus sur le streaming de données dans Azure SQL Edge (préversion)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 15b6b80f9924b050b388d74adc1d67db6a386134
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594508"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Streaming de données dans Azure SQL Edge (préversion)

Azure SQL Edge (préversion) propose deux options d'implémentation du streaming de données. 

1. Déploiement de tâches Azure Stream Analytics créées dans Azure. Pour plus d’informations sur le déploiement de tâches Azure Stream Analytics Edge dans Azure SQL Edge, consultez [Déployer des tâches Azure Stream Analytics](deploy-dacpac.md).
2. Utilisation de la nouvelle fonctionnalité **T-SQL Streaming** pour créer des tâches de streaming dans SQL Edge, sans configurer de tâches de streaming dans Azure. 

Bien qu'il soit possible d'utiliser ces deux options pour implémenter le streaming de données dans SQL Edge, il est vivement recommandé de n'en utiliser qu'une seule. L'utilisation de ces deux options peut entraîner des conditions de concurrence qui ont un impact sur le fonctionnement des opérations de streaming de données.

La suite de ce document fait référence à la nouvelle fonctionnalité, **T-SQL Streaming**, qui permet le streaming de données en temps réel, l'analytique et le traitement des événements pour analyser et traiter rapidement et simultanément d'importants volumes de données de streaming à partir de plusieurs sources. *T-SQL Streaming* repose sur le même moteur de streaming haute performance que celui d'[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) dans Microsoft Azure, et prend en charge un ensemble similaire de fonctionnalités proposées par Azure Stream Analytics s’exécutant en périphérie.

Comme avec Azure Stream Analytics, T-SQL Streaming reconnaît les modèles et les relations des informations extraites d’un certain nombre de sources d’entrée IoT, parmi lesquelles les appareils, les capteurs et les applications. Ces modèles peuvent être utilisés pour déclencher des actions est initier des workflows, comme la création d’alertes, l’envoi d’informations à une solution de rapports, de visualisation, ou le stockage de données transformées pour une utilisation ultérieure. 

Les scénarios suivants sont des exemples d’utilisation de T-SQL Streaming :

* Analyse des flux de données de télémétrie en temps réel à partir d’appareils IoT.
* Analytique en temps réel des données générées à partir de véhicules autonomes et sans pilote.
* Surveillance à distance et maintenance prédictive de ressources stratégiques dans les domaines de l'industrie et de la fabrication.
* Détection des anomalies et/ou reconnaissance des modèles des relevés de capteurs IoT dans une ferme agricole ou énergétique.

## <a name="how-does-t-sql-streaming-work"></a>Comment fonctionne T-SQL Streaming ?

T-SQL Streaming fonctionne de la même manière qu'[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work). Par exemple, il utilise le concept de tâches de streaming pour traiter le streaming de données en temps réel. 

Une tâche Stream Analytics se compose de ce qui suit :

- Entrée de flux - Une entrée de flux définit les connexions à une source de données à partir de laquelle lire le flux de données. Actuellement, Azure SQL Edge prend en charge les types d’entrée de flux suivants :
    - Hub Edge
    - Kafka - Actuellement, la prise en charge des entrées Kafka est uniquement disponible sur les versions Intel/AMD64 d’Azure SQL Edge.

- Sortie de flux - Une sortie de flux définit les connexions à une source de données dans laquelle écrire le flux de données. Actuellement, Azure SQL Edge prend en charge les types de sortie de flux suivants :
    - Hub Edge
    - SQL - La sortie SQL peut être une base de données locale au sein de l’instance SQL Edge ou une instance SQL Server ou Azure SQL Database distante. 
    - Stockage Blob Azure

- Requête de flux - La requête de flux définit la transformation, les agrégations, le filtrage, le tri et les jointures à appliquer au flux d’entrée avant qu’il ne soit écrit dans la sortie de flux. La requête de flux est basée sur le même langage de requête que celui utilisé par Azure Stream Analytics. Pour plus d’informations sur le langage de requête Azure Stream Analytics, consultez [Langage de requête Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> Actuellement, et contrairement à Azure Stream Analytics, T-SQL Streaming ne prend pas en charge l'[utilisation de données de référence pour les recherches](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) ou l'[utilisation d'UDF et d’UDA dans la tâche de flux](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> T-SQL Streaming ne prend en charge qu’un sous-ensemble de la surface d’exposition au langage prise en charge par Azure Stream Analytics. Pour plus d’informations sur le langage de requête Azure Stream Analytics, consultez [Langage de requête Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Limitations et restrictions

Les limitations et restrictions suivantes s'appliquent à T-SQL Streaming. 

- Seule une tâche de streaming peut être active à un moment donné. Les tâches déjà en cours d’exécution doivent être arrêtées avant le démarrage d’une autre tâche.
- Chaque exécution de tâche de streaming est monothread. Si la tâche de streaming contient plusieurs requêtes, chaque requête est évaluée dans l’ordre séquentiel.

## <a name="next-steps"></a>Étapes suivantes

- [Créer une tâche Stream Analytics dans Azure SQL Edge (préversion)](create-stream-analytics-job.md)
- [Afficher les métadonnées associées aux tâches de flux dans Azure SQL Edge (préversion) ](streaming-catalog-views.md)
- [Créer un flux externe](create-external-stream-transact-sql.md)