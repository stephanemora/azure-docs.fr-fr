---
title: Transformer les données
description: Transformez ou traitez des données dans Azure Data Factory à l’aide de Hadoop, d’Azure Machine Learning Studio (classique) ou d’Azure Data Lake Analytics.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 44fc791fd10517ee52fc2e05ea292b889bd95e11
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110496850"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformer des données dans Azure Data Factory

> [!div class="op_single_selector"]
> * [Mappage de flux de données](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Streaming HDInsight](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (classique)](transform-data-using-machine-learning.md) 
> * [Procédure stockée](transform-data-using-stored-procedure.md)
> * [Langage U-SQL du service Analytique Data Lake](transform-data-using-data-lake-analytics.md)
> * [Notebook Synapse](../synapse-analytics/synapse-notebook-activity.md)
> * [Databricks Notebook](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET personnalisé](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Vue d’ensemble
Cet article explique les activités de transformation des données dans Azure Data Factory, que vous pouvez utiliser pour transformer et traiter vos données brutes pour en tirer des prévisions et des informations à l’échelle. Une activité de transformation s’exécute dans un environnement de calcul tel que Azure Databricks ou Azure HDInsight. Elle fournit des liens vers des articles contenant des informations détaillées sur chaque activité de transformation.

Data Factory prend en charge les activités de transformation des données suivantes, qui peuvent être ajoutées à des [pipelines](concepts-pipelines-activities.md) , soit individuellement soit de façon chaînée avec une autre activité.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Transformation en mode natif dans Azure Data Factory avec des flux de données

### <a name="mapping-data-flows"></a>Mappage des flux de données

Les mappages de flux de données sont des transformations de données conçues de manière graphique dans Azure Data Factory. Les flux de données permettent aux ingénieurs de données de développer une logique de transformation graphique des données sans rédiger de code. Les flux de données qui en résultent sont exécutés en tant qu’activités dans les pipelines Azure Data Factory qui utilisent des clusters Spark faisant l’objet d’un scale-out. Les activités de flux de données peuvent être mises en œuvre à l’aide de fonctionnalités de planification, de contrôle, de flux et de supervision Data Factory existantes. Pour plus d’informations, consultez les [flux de données de mappage](concepts-data-flow-overview.md).

### <a name="data-wrangling"></a>Data wrangling

Power Query dans Azure Data Factory vous permet le data wrangling à l’échelle du cloud, ce qui vous permet d’effectuer une préparation de données sans code à l’échelle du cloud de manière itérative. Le data wrangling s’intègre à [Power Query Online](/power-query/) et rend les fonctions Power Query M disponibles pour le data wrangling à l’échelle du cloud via l’exécution Spark. Pour plus d’informations, consultez [Data wrangling dans ADF](wrangling-overview.md).

## <a name="external-transformations"></a>Transformations externes

Si vous le souhaitez, vous pouvez coder manuellement les transformations et gérer l’environnement de calcul externe vous-même.

### <a name="hdinsight-hive-activity"></a>Activité Hive HDInsight
L’activité Hive HDInsight d’un pipeline Data Factory exécute des requêtes Hive sur votre propre cluster ou cluster à la demande HDInsight sous Windows ou Linux. Consultez l’article [Activité Hive](transform-data-using-hadoop-hive.md) pour obtenir plus de détails sur cette activité. 

### <a name="hdinsight-pig-activity"></a>Activité Pig HDInsight
L’activité Pig HDInsight d’un pipeline Data Factory exécute des requêtes Pig sur votre propre cluster ou cluster à la demande HDInsight sous Windows ou Linux. Consultez l’article [Activité Pig](transform-data-using-hadoop-pig.md) pour obtenir plus de détails sur cette activité. 

### <a name="hdinsight-mapreduce-activity"></a>Activité MapReduce HDInsight
L’activité MapReduce de HDInsight dans un pipeline Data Factory exécute des programmes MapReduce sur votre cluster HDInsight propre ou à la demande sous Windows ou Linux. Consultez l’article [Activité MapReduce](transform-data-using-hadoop-map-reduce.md) pour obtenir plus de détails sur cette activité.

### <a name="hdinsight-streaming-activity"></a>Activité de diffusion en continu HDInsight
L’activité de diffusion en continu HDInsight dans un pipeline Data Factory exécute des programmes de diffusion en continu Hadoop sur votre cluster HDInsight, propre ou à la demande, sous Windows ou Linux. Consultez [l’activité de diffusion en continu HDInsight](transform-data-using-hadoop-streaming.md) pour plus d’informations sur cette activité.

### <a name="hdinsight-spark-activity"></a>Activité HDInsight Spark
L’activité Spark HDInsight d’un pipeline Data Factory exécute des programmes Spark sur votre propre cluster HDInsight. Pour plus d’informations, consultez la page [Appeler des programmes Spark à partir de Data Factory](transform-data-using-spark.md). 

### <a name="azure-machine-learning-studio-classic-activities"></a>Activités Azure Machine Learning Studio (classique)
Azure Data Factory vous permet de créer facilement des pipelines qui utilisent un service web Azure Machine Learning Studio (classique) publié pour l’analyse prédictive. À l’aide de l’[activité d’exécution par lot](transform-data-using-machine-learning.md) dans un pipeline Azure Data Factory, vous pouvez appeler un service web Studio (classique) pour effectuer des prédictions sur les données par lot.

Au fil du temps, les modèles prédictifs dans les expériences de notation Studio (classique) doivent être reformés à l’aide de nouveaux jeux de données d’entrée. Une fois que vous avez fini la reformation, vous souhaitez mettre à jour le service Web de notation avec le modèle Machine Learning reformé. Vous pouvez utiliser l’[activité des ressources de mise à jour](update-machine-learning-models.md) pour mettre à jour le service web avec le modèle qui vient d’être formé.  

Pour plus d’informations sur ces activités Studio (classique), consultez [Utiliser des activités Azure Machine Learning Studio (classique)](transform-data-using-machine-learning.md). 

### <a name="stored-procedure-activity"></a>Activité de procédure stockée
Vous pouvez utiliser l’activité Procédure stockée SQL Server dans un pipeline Data Factory pour appeler une procédure stockée dans un des magasins de données suivants : Azure SQL Database, Azure Synapse Analytics ou SQL Server Database dans votre entreprise ou une machine virtuelle Azure. Consultez l’article [Activité de procédure stockée](transform-data-using-stored-procedure.md) pour plus de détails.  

### <a name="data-lake-analytics-u-sql-activity"></a>Activité U-SQL Data Lake Analytics
L’activité U-SQL Data Lake Analytics exécute un script U-SQL sur un cluster Azure Data Lake Analytics. Consultez l’article [Activité U-SQL Data Analytics](transform-data-using-data-lake-analytics.md) pour plus de détails. 

### <a name="synapse-notebook-activity"></a>Activité de notebook Synapse 

L’activité du notebook Azure Synapse dans un pipeline Synapse exécute un notebook Synapse dans votre espace de travail Azure Synapse. Consultez [Transformer des données en exécutant un notebook Synapse](../synapse-analytics/synapse-notebook-activity.md).

### <a name="databricks-notebook-activity"></a>Activité Databricks Notebook

L’activité Azure Databricks Notebook dans un pipeline Data Factory exécute un bloc-notes Databricks dans votre espace de travail Azure Databricks. Azure Databricks est une plateforme gérée pour exécuter Apache Spark. Consultez [Transformer des données en exécutant un bloc-notes Databricks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Activité Databricks Jar

L’activité Azure Databricks Jar dans un pipeline Data Factory exécute un fichier Spark Jar dans votre cluster Azure Databricks. Azure Databricks est une plateforme gérée pour exécuter Apache Spark. Consultez [Transformer des données en exécutant une activité Jar dans Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Activité Databricks Python

L’activité Azure Databricks Python dans un pipeline Data Factory exécute un fichier Python dans votre cluster Azure Databricks. Azure Databricks est une plateforme gérée pour exécuter Apache Spark. Consultez [Transformer des données en exécutant une activité Python dans Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Activité personnalisée
Si vous devez transformer les données d’une manière qui n’est pas prise en charge par Data Factory, créez une activité personnalisée avec votre propre logique de traitement des données et utilisez cette activité dans le pipeline. Vous pouvez configurer l’activité .NET personnalisée pour l’exécuter en utilisant un service Azure Batch ou un cluster Azure HDInsight. Consultez l’article [Utilisation des activités personnalisées](transform-data-using-dotnet-custom-activity.md) pour plus de détails. 

Vous pouvez créer une activité personnalisée pour exécuter des scripts R sur votre cluster HDInsight si R est installé. Consultez la page [Exécuter des scripts R avec Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Environnements de calcul
Vous créez un service lié à l’environnement de calcul puis l’utilisez lorsque vous définissez une activité de transformation. Il existe deux types d'environnements de calcul pris en charge par Data Factory. 

- **À la demande** : dans ce cas, l’environnement de calcul est entièrement géré par Data Factory. Il est automatiquement créé par le service Azure Data Factory avant qu'une tâche de traitement des données ne soit soumise et il est supprimé lorsque la tâche est terminée. Vous pouvez configurer et contrôler les paramètres granulaires de l’environnement de calcul à la demande pour l'exécution de la tâche, la gestion du cluster et les actions d’amorçage. 
- **Apport de votre propre environnement**: dans ce cas, vous pouvez inscrire votre propre environnement de calcul (par exemple un cluster HDInsight) en tant que service lié dans Data Factory. C’est vous qui gérez l'environnement de calcul et le service Data Factory l'utilise pour exécuter les activités. 

Consultez l’article [Services liés de calcul](compute-linked-services.md) pour en savoir plus sur les services de calcul pris en charge par Data Factory. 

## <a name="next-steps"></a>Étapes suivantes
Consultez le didacticiel suivant pour voir un exemple d’utilisation d’une activité de transformation : [Didacticiel - Transformation des données à l’aide de Spark](tutorial-transform-data-spark-powershell.md)
