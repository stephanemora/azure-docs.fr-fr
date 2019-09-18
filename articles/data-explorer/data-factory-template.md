---
title: Utiliser un modèle Azure Data Factory pour la copie en bloc à partir d’une base de données vers Azure Data Explorer
description: Dans cette rubrique, vous apprenez à utiliser un modèle Azure Data Factory pour la copie en bloc à partir d’une base de données vers Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 5a6aebd276ef8658da9ca763be7da5c38a9c772a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873420"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>Utiliser un modèle Azure Data Factory pour la copie en bloc à partir d’une base de données vers Azure Data Explorer

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par de nombreuses sources telles que des applications, des sites web ou des appareils IoT. Azure Data Factory est un service informatique d’intégration de données informatique intégralement managé. Vous pouvez utiliser le service Azure Data Factory pour remplir votre base de données Azure Data Explorer avec les données de votre système existant, et gagner du temps lors de la création de vos solutions d’analyse. 

[Les modèles Azure Data Factory](/azure/data-factory/solution-templates-introduction) sont des pipelines Azure Data Factory prédéfinis qui vous permettent de commencer rapidement à utiliser Data Factory et de réduire le temps de développement pour la création de projets d’intégration de données. Le modèle **Copie en bloc à partir d’une base de données vers Azure Data Explorer** est créé à l’aide des activités **Lookup** et **ForEach**. Vous pouvez utiliser le modèle pour créer un grand nombre de pipelines par base de données ou par table afin de copier plus rapidement les données. 

> [!IMPORTANT]
> * Utilisez le modèle **Copie en bloc à partir d’une base de données vers Azure Data Explorer** pour copier de grandes quantités de données à partir de bases de données comme SQL Server et Google BigQuery vers Azure Data Explorer. 
> * Utilisez [l’outil de copie](data-factory-load-data.md) pour copier quelques tables avec des quantités de données petites ou modérées dans Azure Data Explorer. 

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md)
* [Créer une fabrique de données](data-factory-load-data.md#create-a-data-factory)
* Source des données dans la base de données

## <a name="create-controltabledataset"></a>Créer ControlTableDataset

**ControlTableDataset** indique les données qui seront copiées de la source vers la destination dans le pipeline. Le nombre de lignes indique le nombre total de pipelines nécessaires pour copier les données. **ControlTableDataset** doit être défini dans le cadre de la base de données source.

Exemple de format de table source SQL Server :
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```
    
|Propriété  |Description  | Exemples
|---------|---------| ---------|
|PartitionId   |   ordre de copie | 1  |  
|SourceQuery   |   requête qui indique les données qui seront copiées pendant l’exécution du pipeline | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  nom de la table de destination | MyAdxTable       |  

Si votre **ControlTableDataset** est dans un format différent, créez un **ControlTableDataset** comparable pour votre format.

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>Utiliser la copie en bloc à partir d’une base de données vers un modèle Azure Data Explorer

1. Dans la page **Prise en main**, sélectionnez la vignette **Créer un pipeline à partir d’un modèle** ou sélectionnez l’icône en forme de crayon (onglet **Auteur**) à droite > **+**  >  **Pipeline à partir d’un modèle** pour ouvrir la Galerie de modèles.

    ![Prise en main - Azure Data Factory](media/data-factory-template/adf-get-started.png)

1. Sélectionnez le modèle **Copie en bloc à partir d’une base de données vers Azure Data Explorer**.
 
    ![Sélectionner un pipeline à partir d’un modèle](media/data-factory-template/pipeline-from-template.png)

1.  Dans la fenêtre **Copie en bloc à partir d’une base de données vers Azure Data Explorer**, sélectionnez les jeux de données existants dans la liste déroulante. 

    * **ControlTableDataset** : service lié à la table de contrôle qui indique quelles données sont copiées de la source vers la destination et où elles seront placées dans la destination. 
    * **SourceDataset** : service lié à la base de données source. 
    * **AzureDataExplorerTable** : table Azure Data Explorer. Si le jeu de données n’existe pas, [créez le service lié Azure Data Explorer](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) pour ajouter le jeu de données.
    * Sélectionnez **Utiliser ce modèle**.

    ![configurer le modèle de copie en bloc Azure Data Explorer](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Sélectionnez une zone dans le canevas, en dehors des activités, pour accéder au pipeline de modèle. Sélectionnez **Paramètres** pour entrer les paramètres de la table, y compris le **Nom** de la table de contrôle et la **Valeur par défaut** (noms des colonnes).

    ![Paramètres du pipeline](media/data-factory-template/pipeline-parameters.png)

1.  Sélectionnez l’activité de recherche, **GetPartitionList**, pour afficher les paramètres par défaut. La requête est automatiquement créée.
1.  Sélectionnez l’activité de commande **ForEachPartition**, sélectionnez **Paramètres**
    * **Nombre de lots** : Sélectionnez un nombres entre 1 et 50. Cette sélection détermine le nombre de pipelines qui s’exécutent en parallèle jusqu’à ce que le nombre de lignes **ControlTableDataset** soit atteint. 
    * N’activez PAS la case à cocher **Séquentiel** afin que les lots de pipeline s’exécutent en parallèle.

    ![Paramètres ForEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > La meilleure pratique consiste à exécuter un grand nombre de pipelines en parallèle afin que les données puissent être copiées plus rapidement. Partitionnez les données de la table source et allouez une partition par pipeline, en fonction de la date et de la table, pour accroître l’efficacité.

1. Sélectionnez **Tout valider** pour valider le pipeline ADF. Consultez **Sortie de validation de pipeline**.

    ![Valider les pipelines du modèle](media/data-factory-template/validate-template-pipelines.png)

1. Sélectionnez **Déboguer**, si nécessaire, puis **Ajouter un déclencheur** pour exécuter le pipeline.

    ![Exécuter le pipeline](media/data-factory-template/trigger-run-of-pipeline.png)    


Vous pouvez maintenant utiliser le modèle de **copie en bloc à partir d’une base de données vers Azure Data Explorer** pour copier efficacement de grandes quantités de données à partir de vos bases de données et tables.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez la procédure pour [copier des données dans Azure Data Explorer à l’aide d’Azure Data Factory](data-factory-load-data.md).

* Découvrez le [connecteur Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer) dans Azure Data Factory.

* Découvrez les [requêtes Azure Data Explorer](/azure/data-explorer/web-query-data) pour l’interrogation de données.






