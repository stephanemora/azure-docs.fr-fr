---
title: La copie delta à partir d’une base de données à l’aide d’une table de contrôle avec Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312431"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Copie delta à partir d’une base de données avec une table de contrôle

Cet article décrit un modèle qui est disponible pour charger de façon incrémentielle les lignes nouvelles ou mises à jour à partir d’une table de base de données vers Azure à l’aide d’une table de contrôle externe qui stocke une valeur de limite supérieure.

Ce modèle nécessite que le schéma de la base de données source contient une clé d’incrémentation ou de la colonne horodateur pour identifier les lignes nouvelles ou mises à jour.

>[!NOTE]
> Si vous avez une colonne timestamp dans votre base de données source pour identifier les lignes nouvelles ou mises à jour, mais vous ne souhaitez pas créer une table de contrôle externe à utiliser pour la copie delta, vous pouvez utiliser à la place la [outil de copie de données Azure Data Factory](copy-data-tool.md) pour obtenir un pipeline. Cet outil utilise une heure planifiée de déclencheur en tant que variable pour lire des nouvelles lignes à partir de la base de données source.

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Tout d’abord ce modèle récupère l’ancienne valeur et la compare à la valeur actuelle de la limite. Après cela, il copie uniquement les modifications à partir de la base de données source, basée sur une comparaison entre les deux valeurs de filigrane. Enfin, il stocke la nouvelle valeur de limite supérieure à une table de contrôle externe pour la prochaine de chargement de données delta.

Le modèle contient quatre activités :
- **Recherche** récupère l’ancienne valeur de limite supérieure, qui est stockée dans une table de contrôle externe.
- Un autre **recherche** activité récupère la valeur actuelle de la limite supérieure de la base de données source.
- **Copie** copie des modifications uniquement à partir de la base de données source dans le magasin de destination. La requête qui identifie les modifications dans la base de données source est similaire à « sélectionner * à partir de Data_Source_Table à une TIMESTAMP_Column où > « dernière limite supérieure » et TIMESTAMP_Column < = « limite supérieure en cours » '.
- **SqlServerStoredProcedure** écrit la valeur actuelle de la limite supérieure dans une table de contrôle externe pour la copie delta prochaine fois.

Le modèle définit cinq paramètres :
- *Data_Source_Table_Name* est la table dans la base de données source que vous souhaitez charger des données à partir de.
- *Data_Source_WaterMarkColumn* est le nom de la colonne dans la table source qui a utilisé pour identifier les nouveaux ou mis à jour de lignes. Le type de cette colonne est généralement *datetime*, *INT*, ou similaires.
- *Data_Destination_Folder_Path* ou *Data_Destination_Table_Name* est l’endroit où les données sont copiées vers votre magasin de destination.
- *Control_Table_Table_Name* est la table de contrôle externe qui stocke la valeur de limite supérieure.
- *Control_Table_Column_Name* est la colonne dans la table de contrôle externe qui stocke la valeur de limite supérieure.

## <a name="how-to-use-this-solution-template"></a>Utiliser ce modèle de solution

1. Explorez la source de table vous voulez charger et définir la colonne de limite supérieure qui peut être utilisée pour identifier les lignes nouvelles ou mises à jour. Le type de cette colonne peut être *datetime*, *INT*, ou similaires. La valeur de cette colonne augmente à mesure que les nouvelles lignes sont ajoutées. À partir de la table de source exemple suivant (data_source_table), nous pouvons utiliser le *LastModifytime* colonne que la colonne de limite supérieure.

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
    
2. Créer une table de contrôle dans SQL Server ou de base de données SQL Azure pour stocker la valeur de limite supérieure pour le chargement de données delta. Dans l’exemple suivant, le nom de la table de contrôle est *watermarktable*. Dans ce tableau, *WatermarkValue* est la colonne qui stocke la valeur de limite supérieure, et son type est *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Créer une procédure stockée dans la même instance de SQL Server ou de la base de données SQL Azure que vous avez utilisé pour créer la table de contrôle. La procédure stockée est utilisée pour écrire la nouvelle valeur de limite supérieure dans la table de contrôle externe pour la prochaine de chargement de données delta.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Accédez à la **la copie Delta à partir de la base de données** modèle. Créer un **New** connexion à la base de données source que vous souhaitez copier des données à partir de.

    ![Créer une connexion à la table source](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Créer un **New** connexion au magasin de données de destination que vous souhaitez copier les données.

    ![Créer une connexion à la table de destination](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Créer un **New** connexion à la table de contrôle externe et de la procédure stockée que vous avez créé aux étapes 2 et 3.

    ![Créer une connexion au magasin de données de la table de contrôle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Sélectionnez **utiliser ce modèle**.

     ![Utiliser ce modèle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Vous voyez le pipeline de disponible, comme illustré dans l’exemple suivant :

     ![Passer en revue le pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Sélectionnez **procédure stockée**. Pour **nom de la procédure stockée**, choisissez **[update_watermark]**. Sélectionnez **paramètre d’importation**, puis sélectionnez **ajouter du contenu dynamique**.  

     ![Définissez l’activité de procédure stockée](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Écrivez le contenu  **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}**, puis sélectionnez **Terminer**.  

     ![Écrivez le contenu pour les paramètres de la procédure stockée](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Sélectionnez **déboguer**, entrez la **paramètres**, puis sélectionnez **Terminer**.

    ![Sélectionnez ** ** de débogage](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Résultats similaires à l’exemple suivant sont affichent :

    ![Vérifier le résultat](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Vous pouvez créer des lignes dans votre table source. Voici le langage SQL d’exemple pour créer des lignes :

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. Pour réexécuter le pipeline, sélectionnez **déboguer**, entrez la **paramètres**, puis sélectionnez **Terminer**.

    ![Sélectionnez ** ** de débogage](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Vous consultez que seules les nouvelles lignes ont été copiés vers la destination.

15. (Facultatif :) Si vous avez sélectionné SQL Data Warehouse comme destination des données, vous devez également fournir une connexion à stockage Blob Azure pour l’environnement intermédiaire, ce qui est requis par SQL Data Warehouse Polybase. Assurez-vous que le conteneur a déjà été créé dans le stockage Blob.
    
    ![Configurer Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Étapes suivantes

- [Copie en bloc à partir d’une base de données à l’aide d’une table de contrôle avec Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Copier des fichiers à partir de plusieurs conteneurs avec Azure Data Factory](solution-template-copy-files-multiple-containers.md)
