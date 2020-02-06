---
title: Intégrer Azure Data Lake Storage aux services Azure | Microsoft Docs
description: Apprenez-en davantage sur les services Azure qui s’intègrent à Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: 7f43f69ebdac05b8f99739ea6b51453671b9a70a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024549"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Intégrer Azure Data Lake Storage aux services Azure

Vous pouvez utiliser les services Azure pour ingérer des données, effectuer une analytique et créer des représentations visuelles. Cet article fournit la liste des services Azure pris en charge, et fournit des liens vers des articles qui vous aident à utiliser ces services avec Azure Data Lake Storage Gen2.

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Services Azure qui prennent en charge Azure Data Lake Storage Gen2

Le tableau suivant liste les services Azure qui prennent en charge Azure Data Lake Storage Gen2.

| Service Azure |  Articles connexes |
|---------------|-------------------|
|Azure Data Factory | [Charger des données dans Azure Data Lake Storage Gen2 avec Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Utiliser avec Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Démarrage rapide : Analyser des données dans Azure Data Lake Storage Gen2 à l'aide d'Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Tutoriel : Extraire, transformer et charger des données à l'aide d'Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Tutoriel : Accéder aux données Data Lake Storage Gen2 avec Azure Databricks à l’aide de Spark](data-lake-storage-use-databricks-spark.md) |
|Azure Event Hubs Capture| [Capturer des événements avec Azure Event Hubs dans Stockage Blob Azure ou Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps | [Vue d’ensemble d’Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|[Accéder aux données dans les services de stockage Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Recherche cognitive Azure | [Indexer et parcourir des documents Azure Data Lake Storage Gen2 (préversion)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Stream Analytics| [Démarrage rapide : Créer un travail Stream Analytics à l’aide du portail Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Sortie vers Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [Utiliser Azure Data Box pour migrer les données d’un magasin HDFS local vers Stockage Azure](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Utilisation de l’interface CLI HDFS avec Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Tutoriel : Extraire, transformer et charger des données à l’aide d’Apache Hive sur Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|IoT Hub | [Utiliser le routage des messages IoT Hub pour envoyer des messages appareil-à-cloud à différents points de terminaison](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Analyse des données dans Data Lake Storage Gen2 à l’aide de Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage) |
|SQL Data Warehouse | [Utiliser avec Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS) | [Gestionnaire de connexions Stockage Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les outils que vous pouvez utiliser pour traiter les données dans votre lac de données. Voir [Utiliser Azure Data Lake Storage Gen2 pour le Big Data](data-lake-storage-data-scenarios.md).

- Apprenez-en davantage sur Data Lake Storage Gen2 et comment bien démarrer. Voir [Présentation d’Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).
