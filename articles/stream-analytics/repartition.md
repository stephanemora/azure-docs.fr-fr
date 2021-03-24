---
title: Utiliser le repartitionnement pour optimiser des travaux Azure Stream Analytics
description: Cet article explique comment utiliser le repartitionnement pour optimiser les travaux Azure Stream Analytics qui ne peuvent pas être parallélisés.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95749f2acea6b605cfdba5a4f3d4f5526e751c5a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182534"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Utiliser le repartitionnement pour optimiser le traitement avec Azure Stream Analytics

Cet article explique comment utiliser le repartitionnement pour adapter votre requête Azure Stream Analytics aux scénarios dans lesquels une [parallélisation](stream-analytics-scale-jobs.md) complète n’est pas possible.

Vous ne pourrez peut-être pas utiliser la [parallélisation](stream-analytics-parallelization.md) dans les cas suivants :

* Vous ne contrôlez pas la clé de partition pour votre flux d’entrée.
* Votre source répartit les entrées sur plusieurs partitions qui doivent être fusionnées ultérieurement.

Un repartitionnement, ou redistribution, est nécessaire quand vous traitez des données sur un flux qui n’est pas partitionné selon un schéma d’entrée naturel comme **PartitionId** pour Event Hubs. Quand vous effectuez un repartitionnement, chaque partition peut être traitée de façon indépendante, ce qui vous permet d’effectuer un scale-out de votre pipeline de streaming. 

## <a name="how-to-repartition"></a>Repartitionnement
Vous pouvez repartitionner votre entrée de deux manières :
1. Utiliser une tâche Stream Analytics distincte qui effectue le repartitionnement
2. Utilisez une seule tâche, mais effectuez d’abord le repartitionnement avant votre logique d’analytique personnalisée

### <a name="creating-a-separate-stream-analytics-job-to-repartition-input"></a>Création d’une tâche Stream Analytics distincte pour repartitionner l’entrée
Vous pouvez créer une tâche qui lit les entrées et écrit dans une sortie Event Hub à l’aide d’une clé de partition. Cet Event Hub peut ensuite servir d’entrée pour une autre tâche Stream Analytics où vous implémentez votre logique d’analytique. Lors de la configuration de la sortie de cet Event Hub dans votre tâche, vous devez indiquer la clé de partition par laquelle Stream Analytics repartitionnera vos données. 
```sql
-- For compat level 1.2 or higher
SELECT * 
INTO output
FROM input

--For compat level 1.1 or lower
SELECT *
INTO output
FROM input PARTITION BY PartitionId
```

### <a name="repartition-input-within-a-single-stream-analytics-job"></a>Repartitionner l’entrée au sein d’une seule tâche Stream Analytics
Vous pouvez également introduire une étape dans votre requête qui repartitionne d’abord l’entrée, ce qui peut ensuite être utilisé dans d’autres étapes dans votre requête. Par exemple, si vous souhaitez repartitionner l’entrée en fonction de **DeviceID**, votre requête serait la suivante :
```sql
WITH RepartitionedInput AS 
( 
SELECT * 
FROM input PARTITION BY DeviceID
)

SELECT DeviceID, AVG(Reading) as AvgNormalReading  
INTO output
FROM RepartitionedInput  
GROUP BY DeviceId, TumblingWindow(minute, 1)  
```

L’exemple de requête suivant joint deux flux de données repartitionnées. Lors de la jonction de deux flux de données repartitionnées, les flux doivent avoir la même clé de partition et présenter le même nombre de partitions. Le résultat est un flux qui a le même schéma de partition.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Le schéma de sortie doit correspondre à la clé du schéma de flux et au nombre de flux pour que chaque sous-flux puisse être vidé de façon indépendante. Le flux peut également être de nouveau fusionné et repartitionné selon un schéma différent avant le vidage. Cependant, vous devez éviter cette méthode, car elle augmente la latence générale du traitement et l’utilisation des ressources.

## <a name="streaming-units-for-repartitions"></a>Unités de streaming pour les repartitionnements

Testez et observez l’utilisation des ressources de votre travail pour déterminer le nombre exact de partitions dont vous avez besoin. Le nombre d’[unités de streaming](stream-analytics-streaming-unit-consumption.md) doit être ajusté en fonction des ressources physiques nécessaires à chaque partition. En règle générale, six unités de streaming sont nécessaires pour chaque partition. Si les ressources attribuées au travail sont insuffisantes, le système applique uniquement le repartitionnement s’il est avantageux pour le travail.

## <a name="repartitions-for-sql-output"></a>Repartitionnements pour une sortie SQL

Quand votre travail utilise une base de données SQL pour la sortie, utilisez le repartitionnement explicite pour qu’il corresponde au nombre de partitions optimal et, ainsi, maximiser le débit. Étant donné que SQL fonctionne mieux avec huit enregistreurs, le repartitionnement du flux en huit partitions avant le vidage ou à un point quelconque en amont peut améliorer le niveau de performance du travail. 

Quand il y a plus de huit partitions d’entrée, l’héritage du schéma de partitionnement d’entrée n’est pas toujours une option appropriée. Envisagez d’utiliser [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) dans votre requête pour spécifier explicitement le nombre de générateurs de sortie. 

L’exemple suivant lit l’entrée, qu’elle soit partitionnée naturellement ou non, puis repartitionne le flux en dix partitions conformément à la dimension de DeviceID et vide les données vers la sortie. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Pour plus d'informations, consultez [Sortie d'Azure Stream Analytics dans Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Étapes suivantes

* [Prise en main d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Profiter de la parallélisation de requête dans Azure Stream Analytics](stream-analytics-parallelization.md)
