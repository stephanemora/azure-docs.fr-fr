---
title: Copie delta à partir d’une base de données avec une table de contrôle avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser un modèle de solution pour copier de façon incrémentielle uniquement les nouvelles lignes ou celles mises à jour dans une base de données avec Azure Data Factory.
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
ms.date: 12/24/2018
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966639"
---
# <a name="delta-copy-from-database-with-control-table"></a>Copie delta à partir d’une base de données avec une table de contrôle

Découvrez comment charger de façon incrémentielle sur Azure les changements (nouvelles lignes ou celles mises à jour) apportés à une table de base de données au moyen d’une table de contrôle externe stockant la valeur de limite supérieure.  Le présent modèle est conçu pour ce scénario. 

Ce modèle nécessite que le schéma de base de données source contienne une colonne d’horodatage ou une clé d’incrémentation pour identifier les nouvelles lignes ou celles mises à jour.

Si votre base de données source contient une colonne d’horodatage permettant d’identifier les nouvelles lignes et celles mises à jour, mais que vous ne souhaitez pas créer de table de contrôle externe pour prendre en charge la copie delta, vous pouvez utiliser l’outil Copier des données pour obtenir un pipeline. Celui-ci utilise une heure planifiée par déclencheur comme variable pour lire uniquement les nouvelles lignes dans la base de données source.

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Ce modèle récupère d’abord l’ancienne valeur de limite supérieure, puis la compare à la valeur de limite supérieure actuelle. Il copie ensuite uniquement les changements apportés à la base de données source après comparaison des deux valeurs de limite supérieure.  Une fois terminé, il stocke la nouvelle valeur de limite supérieure dans une table de contrôle externe pour la prochaine opération de chargement de données delta.

Le modèle contient quatre activités :
-   Une activité **Lookup** qui récupère l’ancienne valeur de limite supérieure stockée dans une table de contrôle externe.
-   Une activité **Lookup** qui récupère la valeur de limite supérieure actuelle dans la base de données source.
-   Une activité **Copy** qui copie uniquement les changements apportés à la base de données source vers le magasin de destination. Voici un exemple de requête permettant d’identifier les changements apportés à la base de données source dans l’activité Copy : 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > “last high-watermark” and TIMESTAMP_Column <= “current high-watermark”'.
-   Une activité **SqlServerStoredProcedure** écrit la valeur de limite supérieure actuelle dans une table de contrôle externe pour la prochaine opération de copie delta.

Le modèle définit cinq paramètres :
-   Le paramètre *Data_Source_Table_Name* est le nom de la table de la base de données source à partir de laquelle vous souhaitez charger les données.
-   Le paramètre *Data_Source_WaterMarkColumn* est le nom de la colonne dans la table source qui permet d’identifier les nouvelles lignes ou celles mises à jour. Cette colonne est généralement de type datetime, INT, etc.
-   Le paramètre *Data_Destination_Folder_Path* ou *Data_Destination_Table_Name* correspond à l’emplacement où les données sont copiées dans votre magasin de destination.
-   Le paramètre *Control_Table_Table_Name* est le nom de la table de contrôle externe où la valeur de limite supérieure est stockée.
-   Le paramètre *Control_Table_Column_Name* est le nom de la colonne dans la table de contrôle externe où la valeur de limite supérieure est stockée.

## <a name="how-to-use-this-solution-template"></a>Comment utiliser ce modèle de solution

1. Explorez la table source que vous souhaitez charger et définissez la colonne de limite supérieure à utiliser pour identifier les nouvelles lignes ou celles mises à jour. Cette colonne est généralement de type datetime, INT, etc. Sa capacité augmente à mesure que de nouvelles lignes sont ajoutées.  Dans l’exemple de table source ci-dessous (nom de table : data_source_table), utilisez la colonne *LastModifytime* comme colonne de limite supérieure.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Créez une table de contrôle dans un serveur SQL ou SQL Azure afin de stocker la valeur de limite supérieure pour le chargement de données delta. Dans l’exemple ci-dessous, vous pouvez voir que le nom de la table de contrôle est *watermarktable*. Dans cette table, la colonne utilisée pour stocker la valeur de limite supérieure a pour nom *WatermarkValue* et est de type *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Créez une procédure stockée dans le même serveur SQL ou SQL Azure utilisé pour créer la table de contrôle. La procédure stockée sert à écrire la nouvelle valeur de limite supérieure dans la table de contrôle externe pour la prochaine opération de chargement de données delta.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Accédez au modèle de **copie delta à partir de la base de données**, puis créez une **connexion** à votre base de données source à partir de laquelle les données sont copiées.

    ![Créer une connexion à la table source](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Créez une **connexion** à votre magasin de données de destination où les données sont copiées.

    ![Créer une connexion à la table de destination](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Créez une **connexion** à la table de contrôle externe et à la procédure stockée.  Elle se connecte à la base de données où vous avez créé la table de contrôle et la procédure stockée au cours des étapes 2 et 3.

    ![Créer une connexion au magasin de données de la table de contrôle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Cliquez sur **Utiliser ce modèle**.

     ![Utiliser ce modèle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Le pipeline disponible apparaît dans le panneau, comme le montre l’exemple suivant :

     ![Passer en revue le pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Cliquez sur l’activité Stored Procedure, sélectionnez **Nom de la procédure stockée**, puis cliquez sur **Importer un paramètre** et sur **Ajouter du contenu dynamique**.  

     ![Définir l’activité Stored Procedure](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Écrivez le contenu **@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}**, puis cliquez sur **Terminer**.  

     ![Écrire le contenu du paramètre de la procédure stockée](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Cliquez sur **Déboguer**, entrez les paramètres, puis cliquez sur **Terminer**.

    ![Cliquer sur Déboguer](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Le résultat disponible apparaît dans le panneau, comme le montre l’exemple suivant :

    ![Passer en revue le résultat](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Vous pouvez créer des lignes dans votre table source.  Voici un exemple de code SQL utilisé pour créer des lignes :

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. Réexécutez le pipeline en cliquant sur **Déboguer**, entrez les paramètres, puis cliquez sur **Terminer**.

    ![Cliquer sur Déboguer](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. Vous pouvez voir que seules les nouvelles lignes ont été copiées dans la destination.

15. (Facultatif) Si vous sélectionnez SQL Data Warehouse comme destination des données, vous devez également entrer la connexion d’un stockage d’objets blob Azure comme processus de site, conformément aux exigences de SQL Data Warehouse Polybase.  Vérifiez que le conteneur est créé dans le stockage d’objets blob.  
    
    ![Configurer Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)