---
title: Définition des types de données
description: Recommandations pour la définition des types de données de table dans Azure SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 020b6fc08dad0dacb51215eca6f7baf127a9dba6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351311"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Types de tables de données dans Azure SQL Data Warehouse
Recommandations pour la définition des types de données de table dans Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Quels sont les types de données ?

SQL Data Warehouse prend en charge les types de données les plus couramment utilisés. Pour obtenir la liste des types de données pris en charge, consultez les [types de données](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) dans l’instruction CREATE TABLE. 

## <a name="minimize-row-length"></a>Réduction de la longueur de ligne
En réduisant la taille des types de données, vous réduisez la longueur de ligne, ce qui entraîne une amélioration des performances de requête. Utilisez le plus petit type de données qui fonctionne pour vos données. 

- Évitez de définir des colonnes de caractères ayant une grande longueur par défaut. Par exemple, si la valeur la plus longue est de 25 caractères, définissez la colonne en tant que VARCHAR(25). 
- Évitez d’utiliser [NVARCHAR][NVARCHAR] lorsque vous avez uniquement besoin de VARCHAR.
- Lorsque c’est possible, utilisez NVARCHAR(4000) ou VARCHAR(8000) au lieu de NVARCHAR(MAX) ou VARCHAR(MAX).

Si vous utilisez des tables externes PolyBase pour charger vos tables, la longueur définie pour la ligne de table ne doit pas dépasser 1 Mo. Lorsqu’une ligne avec des données de longueur variable dépasse 1 Mo, vous pouvez télécharger la ligne avec BCP, mais pas avec PolyBase.

## <a name="identify-unsupported-data-types"></a>Identification des types de données non pris en charge
Si vous migrez votre base de données à partir d’une autre base de données SQL, vous pouvez rencontrer certains types de données qui ne sont pas pris en charge sur SQL Data Warehouse. Utilisez cette requête pour identifier les types de données non pris en charge dans votre schéma SQL existant.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Solutions de contournement pour les types de données non pris en charge

La liste suivante répertorie les types de données non pris en charge par SQL Data Warehouse, et donne des alternatives que vous pouvez utiliser à la place de ces types de données.

| Type de données non pris en charge | Solution de contournement |
| --- | --- |
| [geometry](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Geography](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Fractionnez la colonne en plusieurs colonnes fortement typées. |
| [table](/sql/t-sql/data-types/table-transact-sql) |Appliquez une conversion vers des tables temporaires. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Corrigez le code à utiliser [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) et la fonction [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql). Seules les constantes sont prises en charge en tant que valeurs par défaut, par conséquent le paramètre current_timestamp ne peut pas être défini comme une contrainte par défaut. Si vous devez migrer les valeurs de version de ligne à partir d’une colonne de type horodatage, utilisez le paramètre [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) ou [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) pour les valeurs de version de ligne NOT NULL ou NULL. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [type défini par l’utilisateur](/sql/relational-databases/native-client/features/using-user-defined-types) |Revenez au type de données natif lorsque c’est possible. |
| valeurs par défaut | Les valeurs par défaut prennent uniquement en charge des littéraux et des constantes. |


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le développement des tables, consultez [Vue d’ensemble des tables](sql-data-warehouse-tables-overview.md).
