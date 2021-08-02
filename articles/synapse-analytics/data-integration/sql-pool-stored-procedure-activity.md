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
ms.openlocfilehash: 70942f16d58fecb83c4373f951f5dd20cfcadc0b
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110495050"
---
# <a name="transform-data-by-using-sql-pool-stored-procedure-activity-in-azure-synapse-analytics"></a>Transformer des données à l’aide de l’activité de procédure stockée de pool SQL dans Azure Synapse Analytics

<Token>**s’applique À :** ![non pris en charge](../media/applies-to/no.png)Azure Data Factory![ pris en charge](../media/applies-to/yes.png) Azure Synapse Analytics </Token> 

Vous utilisez des activités de transformation de données dans un [pipeline](../../data-factory/concepts-pipelines-activities.md) pour transformer et traiter des données brutes en prévisions et analyses. Cet article s’ajoute à l’article [Transformer des données](../../data-factory/transform-data.md) qui présente une vue d’ensemble de la transformation de données et des activités de transformation prises en charge.

Dans Azure Synapse Analytics, vous pouvez utiliser l’activité de procédure stockée de pool SQL pour appeler une procédure stockée dans un pool SQL dédié.

## <a name="syntax-details"></a>Détails de la syntaxe

L’activité de procédure stockée de pool SQL prend en charge les paramètres suivants :

| Propriété                  | Description                              | Obligatoire |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Nom de l’activité                     | Oui      |
| description               | Texte décrivant la raison motivant l’activité. | Non       |
| type                      | Pour l’activité de procédure stockée de pool SQL, le type d’activité est **SqlPoolStoredProcedure**. | Oui      |
| sqlpool         | Référence à un [pool SQL dédié](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) dans l’espace de travail Azure Synapse actuel. | Oui      |
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
