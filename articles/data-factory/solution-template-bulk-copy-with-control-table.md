---
title: Copie à partir d’une base de données en bloc avec une table de contrôle avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser un modèle de solution pour copier des données en bloc à partir d’une base de données à l’aide d’une table de contrôle externe pour stocker une liste de partition des tables sources à l’aide d’Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635386"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Copie en bloc à partir d’une base de données avec une table de contrôle

Pour copier des données à partir d’un entrepôt de données dans Oracle Server, Netezza, Teradata ou SQL Server vers Azure SQL Data Warehouse, vous devez charger de grandes quantités de données à partir de plusieurs tables. En règle générale, les données doit être partitionnées dans chaque table, afin que vous pouvez charger des lignes avec plusieurs threads en parallèle à partir d’une seule table. Cet article décrit un modèle à utiliser dans ces scénarios.

 >! Remarque Si vous souhaitez copier des données à partir d’un petit nombre de tables avec volume de données relativement faible vers SQL Data Warehouse, il est plus efficace d’utiliser le [outil de copie de données Azure Data Factory](copy-data-tool.md). Le modèle qui est décrite dans cet article est plus que vous avez besoin pour ce scénario.

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Ce modèle récupère une liste de partitions de base de données source à copier à partir d’une table de contrôle externe. Ensuite, il effectue une itération sur chaque partition dans la base de données source et copie les données vers la destination.

Le modèle comporte trois activités :
- **Recherche** récupère la liste des partitions de base de données que d’un tableau de contrôle externe.
- **ForEach** Obtient la liste de la partition à partir de l’activité de recherche et itère au sein de chaque partition à l’activité de copie.
- **Copie** copie chaque partition à partir du magasin de base de données source vers le magasin de destination.

Le modèle définit cinq paramètres :
- *Control_Table_Name* est votre table de contrôle externe, qui stocke la liste de partition pour la base de données source.
- *Control_Table_Schema_PartitionID* est le nom du nom de colonne dans votre table de contrôle externe qui stocke chaque ID de partition. Assurez-vous que l’ID de partition est unique pour chaque partition dans la base de données source.
- *Control_Table_Schema_SourceTableName* est votre table de contrôle externe qui stocke chaque nom de table à partir de la base de données source.
- *Control_Table_Schema_FilterQuery* est le nom de la colonne dans votre table de contrôle externe qui stocke la requête de filtre pour obtenir les données à partir de chaque partition dans la base de données source. Par exemple, si les données sont partitionnées par année, la requête est stockée dans chaque ligne peut être semblable à « sélectionner * à partir de la source de données où LastModifytime > = '' 2015-01-01 00:00:00 '' et LastModifytime < = '' 2015-12-31 23:59:59.999'' '.
- *Data_Destination_Folder_Path* est le chemin d’accès où les données sont copiées dans votre magasin de destination. Ce paramètre est uniquement visible si la destination que vous choisissez est basée sur le fichier de stockage. Si vous choisissez SQL Data Warehouse en tant que la banque de destination, ce paramètre n’est pas obligatoire. Mais les noms de table et le schéma dans SQL Data Warehouse doivent être le même que celles de la base de données source.

## <a name="how-to-use-this-solution-template"></a>Utiliser ce modèle de solution

1. Créer une table de contrôle dans SQL Server ou de base de données SQL Azure pour stocker la liste de partition de base de données source pour la copie en bloc. Dans l’exemple suivant, il existe cinq partitions dans la base de données source. Trois partitions concernent la *datasource_table*, et deux autres pour le *project_table*. La colonne *LastModifytime* est utilisé pour partitionner les données de table *datasource_table* à partir de la base de données source. La requête qui est utilisée pour lire la première partition est « sélectionner * à partir de datasource_table où LastModifytime > = '' 2015-01-01 00:00:00 '' et LastModifytime < = '' 2015-12-31 23:59:59.999'' '. Vous pouvez utiliser une requête similaire pour lire les données à partir d’autres partitions.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Accédez à la **copie en bloc à partir de la base de données** modèle. Créer un **New** connexion à la table de contrôle externe que vous avez créé à l’étape 1.

    ![Créer une connexion à la table de contrôle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Créer un **New** connexion à la base de données source que vous copiez des données à partir de.

     ![Créer une connexion à la base de données source](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Créer un **New** que vous copiez des données à stocker les connexion aux données de destination.

    ![Créer une connexion au magasin de destination](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Sélectionnez **utiliser ce modèle**.

    ![Utiliser ce modèle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Vous voyez le pipeline, comme indiqué dans l’exemple suivant :

    ![Passer en revue le pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Sélectionnez **déboguer**, entrez la **paramètres**, puis sélectionnez **Terminer**.

    ![Cliquez sur ** ** de débogage](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Vous constatez des résultats qui sont similaires à l’exemple suivant :

    ![Vérifier le résultat](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Facultatif) Si vous avez choisi de SQL Data Warehouse comme destination des données, vous devez entrer une connexion au stockage Blob Azure pour l’environnement intermédiaire, comme requis par SQL Data Warehouse Polybase. Assurez-vous que le conteneur dans le stockage Blob a déjà été créé.
    
    ![Paramètre Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)
