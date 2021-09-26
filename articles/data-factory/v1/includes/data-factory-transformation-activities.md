---
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 850333ff1d0a2668dcd0553f2d03562fb2c046a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624409"
---
Azure Data Factory prend en charge les activités de transformation suivantes, qui peuvent être ajoutées à des pipelines, soit individuellement soit de façon chaînée avec une autre activité.

| Activités de transformation des données | Environnement de calcul |
|:--- |:--- |
| [Hive](../data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Diffusion en continu Hadoop](../data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../data-factory-spark.md) | HDInsight [Hadoop] |
| [Activités ML Studio (classique) : Batch Execution et Update Resource](../data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Procédure stockée](../data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics ou SQL Server |
| [Langage U-SQL du service Analytique Data Lake](../data-factory-usql-activity.md) |Service Analytique Azure Data Lake |
| [DotNet](../data-factory-use-custom-activities.md) |HDInsight [Hadoop] ou Azure Batch |

> [!NOTE]
> Vous pouvez utiliser l'activité MapReduce pour exécuter des programmes Spark sur votre cluster HDInsight Spark. Consultez la page [Appeler des programmes Spark à partir d'Azure Data Factory](../data-factory-spark.md) pour plus d'informations.
> Vous pouvez créer une activité personnalisée pour exécuter des scripts R sur votre cluster HDInsight si R est installé. Consultez la page [Exécuter des scripts R avec Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample).
> 
> 

