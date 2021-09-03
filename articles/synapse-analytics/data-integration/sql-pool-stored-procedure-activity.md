---
title: Transformer des données à l’aide de l’activité de procédure stockée de pool SQL
description: Explique comment utiliser l’activité de procédure stockée de pool SQL pour appeler une procédure stockée dans Azure Synapse Analytics.
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: pipeline
author: linda33wj
ms.author: jingwang
ms.reviewer: jrasnick
ms.date: 05/13/2021
ms.openlocfilehash: f7f697b9df78c44efb7a266d1a92bdd9ffe31753
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112967688"
---
# <a name="transform-data-by-using-sql-pool-stored-procedure-activity-in-azure-synapse-analytics"></a>Transformer des données à l’aide de l’activité de procédure stockée de pool SQL dans Azure Synapse Analytics

<Token>**s’applique À :** ![non pris en charge](../media/applies-to/no.png)Azure Data Factory![ pris en charge](../media/applies-to/yes.png) Azure Synapse Analytics</Token> 

Vous utilisez des activités de transformation de données dans un [pipeline](../../data-factory/concepts-pipelines-activities.md) pour transformer et traiter des données brutes en prévisions et analyses. Cet article s’ajoute à l’article [Transformer des données](../../data-factory/transform-data.md) qui présente une vue d’ensemble de la transformation de données et des activités de transformation prises en charge.

Dans Azure Synapse Analytics, vous pouvez utiliser l’activité de procédure stockée de pool SQL pour appeler une procédure stockée dans un pool SQL dédié.

## <a name="syntax-details"></a>Détails de la syntaxe

Les paramètres suivants sont pris en charge par l’activité de procédure stockée de pool SQL :

| Propriété                  | Description                              | Obligatoire |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nom de l’activité                     | Oui      |
| description               | Texte décrivant la raison motivant l’activité. | Non       |
| type                      | Pour l’activité de procédure stockée de pool SQL, le type d’activité est **SqlPoolStoredProcedure** | Oui      |
| sqlpool         | Référence à un [pool SQL dédié](../sql/overview-architecture.md) dans l’espace de travail Azure Synapse actuel. | Oui      |
| storedProcedureName       | Spécifiez le nom de la procédure stockée à appeler. | Oui      |
| storedProcedureParameters | Spécifiez les valeurs des paramètres de procédure stockée. Utilisez `"param1": { "value": "param1Value","type":"param1Type" }` pour transmettre les valeurs des paramètres et leur type pris en charge par la source de données. Pour passer la valeur Null en paramètre, utilisez `"param1": { "value": null }` (tout en minuscules). | Non       |

Exemple :

```json
{
    "name": "SQLPoolStoredProcedureActivity",
    "description":"Description",
    "type": "SqlPoolStoredProcedure",
    "sqlPool": {
        "referenceName": "DedicatedSQLPool",
        "type": "SqlPoolReference"
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

## <a name="next-steps"></a>Étapes suivantes
 
- [Pipeline et activité](../../data-factory/concepts-pipelines-activities.md)
- [Pool SQL dédié](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
