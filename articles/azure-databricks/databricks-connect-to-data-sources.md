---
title: 'Se connecter à plusieurs sources de données à partir d’Azure Databricks '
description: Découvrez comment vous connecter à Azure SQL Database, Azure Data Lake Store, Stockage Blob, Cosmos DB, Event Hubs et Azure SQL Data Warehouse à partir d’Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 79a821a4c8fe4cb2d048f0dcb0a6e091462a1779
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548789"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Se connecter à des sources de données à partir d’Azure Databricks

Cet article contient des liens vers toutes les sources de données différentes dans Azure qui peuvent être connectées à Azure Databricks. Suivez les exemples de ces liens pour extraire des données à partir des sources de données Azure (par exemple, Stockage Blob Azure, Azure Event Hubs, etc.) dans un cluster Azure Databricks, puis exécutez des travaux analytiques sur ces données. 

## <a name="prerequisites"></a>Conditions préalables requises

* Vous devez disposer d’un espace de travail Azure Databricks et d’un cluster Spark. Suivez les instructions contenues dans [Démarrage rapide : Exécuter une tâche Spark sur Azure Databricks à l’aide du portail Azure](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Sources de données pour Azure Databricks

La liste suivante indique les sources de données dans Azure que vous pouvez utiliser avec Azure Databricks. Pour obtenir la liste complète des sources de données que vous pouvez utiliser avec Azure Databricks, consultez [Data sources for Azure Databricks](/azure/databricks/data/data-sources/index) (Sources de données d’Azure Databricks).

- [Base de données Azure SQL](/azure/databricks/data/data-sources/sql-databases)

    Cette rubrique fournit l’API DataFrame pour la connexion aux bases de données SQL à l’aide de JDBC, et indique comment contrôler le parallélisme des lectures via l’interface JDBC. Elle contient également des exemples détaillés d’utilisation de l’API Scala et des exemples Python et Spark SQL abrégés.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Ce lien contient des exemples d’utilisation du principal du service Azure Active Directory pour s’authentifier auprès d’Azure Data Lake Storage. Elle fournit également des instructions pour accéder aux données dans Azure Data Lake Storage à partir d’Azure Databricks.

- [Stockage Blob Azure](/azure/databricks/data/data-sources/azure/azure-storage)

    Cette rubrique contient des exemples indiquant comment accéder directement à Stockage Blob Azure à partir d’Azure Databricks à l’aide de la clé d’accès ou de la signature d’accès partagé d’un conteneur donné. Cette rubrique fournit également des informations sur la façon d’accéder à Stockage Blob Azure à partir d’Azure Databricks à l’aide de l’API RDD.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Cette rubrique contient des instructions sur l’utilisation du [connecteur Spark pour Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) à partir d’Azure Databricks pour accéder aux données d’Azure Cosmos DB.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Cette rubrique contient des instructions sur l’utilisation du [connecteur Spark pour Azure Event Hubs](https://github.com/Azure/azure-event-hubs-spark) à partir d’Azure Databricks pour accéder aux données d’Azure Event Hubs.

- [Azure SQL Data Warehouse](/azure/synapse-analytics/sql-data-warehouse/)

    Cette rubrique contient des instructions sur l’utilisation du connecteur Azure SQL Data Warehouse pour se connecter à partir d’Azure Databricks.
    

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les sources à partir desquelles vous pouvez importer des données dans Azure Databricks, consultez [Spark Data Sources](/azure/databricks/data/data-sources/index) (Sources de données Spark)


