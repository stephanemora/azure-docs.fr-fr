---
title: Copie en bloc à partir d’une base de données avec une table de contrôle avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser un modèle de solution pour copier entièrement des données en bloc à partir d’une base de données à l’aide d’une table de contrôle externe pour stocker la liste de partitions des tables sources avec Azure Data Factory.
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
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966705"
---
# <a name="bulk-copy-from-database-with-control-table"></a>Copie en bloc à partir d’une base de données avec une table de contrôle

Pour copier dans Azure des données de votre entrepôt de données (serveur Oracle, Netezza, Teradata ou SQL), vous devez charger de grandes quantités de données provenant de plusieurs tables dans des sources de données. Dans la plupart des cas, les données doivent être partitionnées dans chaque table pour que vous puissiez charger des lignes avec plusieurs threads en parallèle à partir d’une seule table. Le présent modèle est conçu pour ce scénario. 

Si vous souhaitez copier des données de taille réduite à partir d’un petit nombre de tables, il est plus efficace d’accéder à l’outil de copie des données pour avoir une seule activité Copy ou une activité Foreach et Copy dans votre pipeline. Ce modèle est amplement suffisant pour ce cas d’utilisation simple.

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Ce modèle récupère la liste des partitions de base de données source à partir d’une table de contrôle externe à copier dans le magasin de destination, puis effectue une itération sur chaque partition dans la base de données source et effectue l’opération de copie de données.

Le modèle contient trois activités :
-   Une activité **Lookup** qui récupère la liste de partitions de la base de données source à partir d’une table de contrôle externe.
-   Une activité **ForEach** qui obtient la liste de partitions de l’activité Lookup, puis itère chacune d’elles vers l’activité Copy.
-   Une activité **Copy** qui copie chaque partition du magasin de base de données source dans le magasin de destination.

Le modèle définit cinq paramètres :
-   Le paramètre *Control_Table_Name* est le nom de votre table de contrôle externe. La table de contrôle stocke la liste de partitions de la base de données source.
-   Le paramètre *Control_Table_Schema_PartitionID* est le nom de colonne dans votre table de contrôle externe utilisé pour stocker chaque ID de partition. Veillez à ce que l’ID de partition soit unique pour chaque partition dans la base de données source.
-   Le paramètre *Control_Table_Schema_SourceTableName* est le nom de colonne dans votre table de contrôle externe utilisé pour stocker chaque nom de table à partir de la base de données source.
-   Le paramètre *Control_Table_Schema_FilterQuery* est le nom de colonne dans votre table de contrôle externe utilisé pour stocker la requête de filtre servant à obtenir les données de chaque partition dans la base de données source. Par exemple, si vous avez partitionné les données par année, la requête stockée dans chaque ligne peut ressembler à ceci : 'select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''.
-   Le paramètre *Data_Destination_Folder_Path* est le chemin du dossier où les données sont copiées dans votre magasin de destination.  Ce paramètre est visible uniquement quand la destination que vous choisissez est un magasin de stockage basé sur des fichiers.  Si vous choisissez SQL Data Warehouse comme magasin de destination, aucun paramètre ne doit être entré ici. Cependant, les noms de table et le schéma dans l’entrepôt de données SQL doivent être identiques à ceux de la base de données source.

## <a name="how-to-use-this-solution-template"></a>Comment utiliser ce modèle de solution

1. Créez une table de contrôle dans SQL Server ou SQL Azure afin de stocker la liste de partitions de la base de données source pour la copie en bloc.  Dans l’exemple ci-dessous, vous pouvez voir que votre base de données source contient cinq partitions, dont trois pour une table (*datasource_table*) et deux pour une autre table (*project_table*). La colonne *LastModifytime* permet de partitionner les données de la table *datasource_table* à partir de la base de données source. La requête utilisée pour lire la première partition est 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''.  Vous pouvez également voir une requête similaire pour lire les données d’autres partitions. 

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

2. Accédez au modèle de **copie en bloc à partir d’une base de données** et créez une **connexion** à votre table de contrôle externe.  Cette connexion est établie à la base de données où vous avez créé la table de contrôle au cours de l’étape 1.

    ![Créer une connexion à la table de contrôle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Créez une **connexion** à la base de données source à partir de laquelle les données sont copiées.

     ![Créer une connexion à la base de données source](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Créez une **connexion** à votre magasin de données de destination où les données sont copiées.

    ![Créer une connexion au magasin de destination](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Cliquez sur **Utiliser ce modèle**.

    ![Utiliser ce modèle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Le pipeline disponible apparaît dans le panneau, comme le montre l’exemple suivant :

    ![Passer en revue le pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Cliquez sur **Déboguer**, entrez les paramètres, puis cliquez sur **Terminer**.

    ![Cliquer sur Déboguer](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Le résultat disponible apparaît dans le panneau, comme le montre l’exemple suivant :

    ![Passer en revue le résultat](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Facultatif) Si vous sélectionnez SQL Data Warehouse comme destination des données, vous devez également entrer la connexion d’un stockage d’objets blob Azure comme processus de site, conformément aux exigences de SQL Data Warehouse Polybase.  Vérifiez que le conteneur dans le stockage d’objets blob est déjà créé.  
    
    ![Paramètre Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)