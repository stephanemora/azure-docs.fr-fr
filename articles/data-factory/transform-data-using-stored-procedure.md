---
title: Transformer des données à l’aide de l’activité de procédure stockée
titleSuffix: Azure Data Factory & Azure Synapse
description: Explique comment utiliser l’activité de procédure stockée SQL Server pour appeler une procédure stockée dans Azure SQL Database/Data Warehouse à partir d’un pipeline Azure Data Factory ou Synapse Analytics.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 30e40c20aa8e11add35b270baf867a5fd5e46058
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131016565"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory-or-synapse-analytics"></a>Transformer des données à l’aide de l’activité de procédure stockée SQL Server dans Azure Data Factory ou Synapse Analytics
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-stored-proc-activity.md)
> * [Version actuelle](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vous utilisez des activités de transformation dans un [pipeline](concepts-pipelines-activities.md) Data Factory ou Synapse pour transformer et traiter des données brutes en prévisions et en analyses. L’activité de procédure stockée est l’une des activités de transformation prises en charge par les pipelines. Cet article s’ajoute à l’article [Transformer des données](transform-data.md) qui présente une vue d’ensemble de la transformation de données et des activités de transformation prises en charge.

> [!NOTE]
> Si vous découvrez Azure Data Factory, lisez la [présentation d’Azure Data Factory](introduction.md) et suivez le tutoriel : [Tutoriel : Transformer des données](tutorial-transform-data-spark-powershell.md) avant de lire cet article.  Pour en savoir plus sur Synapse Analytics, consultez [Qu’est-ce qu’Azure Synapse Analytics](../synapse-analytics/overview-what-is.md).

Vous pouvez utiliser l’activité de procédure stockée pour appeler une procédure stockée dans l’une des banques de données suivantes dans votre entreprise ou sur une machine virtuelle Azure : 

- Azure SQL Database
- Azure Synapse Analytics
- Base de données SQL Server  Si vous utilisez SQL Server, installez le runtime d’intégration auto-hébergé sur l’ordinateur qui héberge la base de données ou sur un autre ordinateur ayant accès à la base de données. Le runtime d’intégration auto-hébergé est un composant qui connecte des sources de données locales ou se trouvant sur une machine virtuelle Azure à des services cloud de manière gérée et sécurisée. Pour plus d’informations, consultez l’article [Runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md).

> [!IMPORTANT]
> Lorsque vous copiez des données dans Azure SQL Database ou SQL Server, vous pouvez configurer l’élément **SqlSink** dans l’activité de copie pour appeler une procédure stockée en utilisant la propriété **sqlWriterStoredProcedureName**. Pour plus d’informations sur la propriété, consultez les articles suivants sur les connecteurs : [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). L’appel d’une procédure stockée lors de la copie de données dans Azure Synapse Analytics à l’aide d’une activité de copie n’est pas pris en charge. Toutefois, vous pouvez utiliser l’activité de procédure stockée pour appeler une procédure stockée dans Azure Synapse Analytics. 
>
> Lors de la copie de données à partir d’Azure SQL Database, de SQL Server ou d’Azure Synapse Analytics, vous pouvez configurer **SqlSource** dans l’activité de copie pour appeler une procédure stockée afin de lire les données à partir de la base de données source en utilisant la propriété **sqlReaderStoredProcedureName**. Pour plus d’informations, consultez les articles suivants sur les connecteurs : [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Détails de la syntaxe
Voici le format JSON pour la définition d’une activité de procédure stockée :

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

Le tableau suivant décrit ces paramètres JSON :

| Propriété                  | Description                              | Obligatoire |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nom de l’activité                     | Oui      |
| description               | Texte décrivant la raison motivant l’activité. | Non       |
| type                      | Pour l’activité de procédure stockée, le type d’activité est **SqlServerStoredProcedure**. | Oui      |
| linkedServiceName         | Référence au service **Azure SQL Database**, **Azure Synapse Analytics** ou **SQL Server** enregistré en tant que service lié dans Data Factory. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md). | Oui      |
| storedProcedureName       | Spécifiez le nom de la procédure stockée à appeler. | Oui      |
| storedProcedureParameters | Spécifiez les valeurs des paramètres de procédure stockée. Utilisez `"param1": { "value": "param1Value","type":"param1Type" }` pour transmettre les valeurs des paramètres et leur type pris en charge par la source de données. Pour passer la valeur Null en paramètre, utilisez `"param1": { "value": null }` (tout en minuscules). | Non       |

## <a name="parameter-data-type-mapping"></a>Mappage du type de données du paramètre
Le type de données que vous spécifiez pour le paramètre est le type de service interne correspondant à celui de la source de données que vous utilisez. Vous trouverez les mappages de types de données pour votre source de données décrits dans la documentation relative aux connecteurs. Par exemple :

- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#data-type-mapping-for-azure-synapse-analytics)
- [Mappage de types de données Azure SQL Database](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database)
- [Mappage de types de données Oracle](connector-oracle.md#data-type-mapping-for-oracle)
- [Mappages de types de données SQL Server](connector-sql-server.md#data-type-mapping-for-sql-server)

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants qui expliquent comment transformer des données par d’autres moyens : 

* [Activité U-SQL](transform-data-using-data-lake-analytics.md)
* [Activité Hive](transform-data-using-hadoop-hive.md)
* [Activité pig](transform-data-using-hadoop-pig.md)
* [Activité MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Activité de diffusion en continu Hadoop](transform-data-using-hadoop-streaming.md)
* [Activité Spark](transform-data-using-spark.md)
* [Activité personnalisée .NET](transform-data-using-dotnet-custom-activity.md)
* [Activité de procédure stockée](transform-data-using-stored-procedure.md)
