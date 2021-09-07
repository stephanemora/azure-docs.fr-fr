---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 79f3d2cf2e9b966d8486560efc7ec67b09c7ed4b
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397679"
---
Azure Data Factory prend en charge les activités de transformation suivantes, qui peuvent être ajoutées à des pipelines, soit individuellement soit de façon chaînée avec une autre activité.

| Activités de transformation des données | Environnement de calcul |
|:--- |:--- |
| [Hive](../data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Diffusion en continu Hadoop](../data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../data-factory-spark.md) | HDInsight [Hadoop] |
| [Activités de ML Studio (classique) : exécution par lot et mise à jour de ressource](../data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Procédure stockée](../data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics ou SQL Server |
| [Langage U-SQL du service Analytique Data Lake](../data-factory-usql-activity.md) |Service Analytique Azure Data Lake |
| [DotNet](../data-factory-use-custom-activities.md) |HDInsight [Hadoop] ou Azure Batch |

> [!NOTE]
> Vous pouvez utiliser l'activité MapReduce pour exécuter des programmes Spark sur votre cluster HDInsight Spark. Consultez la page [Appeler des programmes Spark à partir d'Azure Data Factory](../data-factory-spark.md) pour plus d'informations.
> Vous pouvez créer une activité personnalisée pour exécuter des scripts R sur votre cluster HDInsight si R est installé. Consultez la page [Exécuter des scripts R avec Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample).
> 
> 

