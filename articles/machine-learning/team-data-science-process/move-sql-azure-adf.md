---
title: Données à partir d’un serveur SQL local vers SQL Azure avec Azure Data Factory - Processus Team Data Science
description: Configuration d’un pipeline ADF composé de deux activités de migration des données qui déplacent quotidiennement les données entre des bases de données locales et sur le cloud.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b64aa6c0e6e0e3bf449d44996df3223b12a69923
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982411"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Déplacement de données à partir d’un serveur SQL local vers SQL Azure avec Azure Data Factory

Cet article explique comment déplacer des données à partir d’une base de données SQL Server locale vers une base de données SQL Azure via le Stockage Blob Azure à l’aide d’Azure Data Factory (ADF) : cette méthode est une approche héritée prise en charge qui présente les avantages d’une copie intermédiaire répliquée, même si [nous vous suggérons de consulter notre page Datamigration pour connaître les options les plus récentes](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

Pour accéder à un tableau résumant les différentes options de déplacement de données dans une base de données Azure SQL, consultez [Déplacer des données dans une base de données Azure SQL pour Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Présentation : Qu’est-ce qu’ADF et quand doit-il être utilisé pour migrer des données ?
Azure Data Factory est un service d’intégration de données dans le cloud entièrement géré qui gère et automatise le déplacement et la transformation des données. Le concept clé du modèle ADF est le pipeline. Un pipeline est un regroupement logique d’activités, chacune d'elles définissant les actions à effectuer sur les données contenues dans des groupes de données. Les services liés sont utilisés pour définir les informations nécessaires à Data Factory pour se connecter à des ressources de données.

Avec ADF, les services de traitement de données existants peuvent être composés dans des pipelines de données, à disponibilité élevée et gérés dans le cloud. Ces pipelines de données peuvent être soumis à planification pour la réception, la préparation, la transformation, l’analyse et la publication de données. ADF gère et orchestre les données et les dépendances de traitement complexes. Les solutions peuvent être rapidement créées et déployées dans le cloud, afin de connecter un nombre croissant de sources de données locales et cloud.

Utilisez plutôt ADF :

* lorsque les données doivent être migrées en permanence dans un scénario hybride qui accède à la fois aux ressources locales et cloud ;
* lorsque les données sont traitées ou doivent être modifiées ou complétées par une logique métier lors de leur migration.

ADF permet la planification et la surveillance des travaux à l'aide de scripts JSON simples qui gèrent le déplacement des données sur une base périodique. ADF dispose également d'autres fonctionnalités comme la prise en charge des opérations complexes. Pour plus d'informations sur ADF, consultez la documentation relative à [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Scénario
Nous allons configurer un pipeline ADF qui se compose de deux activités de migration de données. Ensemble, ces activités déplacent les données quotidiennement entre une base de données SQL locale et une base de données Azure SQL dans le cloud. Les deux activités sont :

* copie de données depuis une base de données SQL Server locale vers un compte de stockage d’objets blob Azure ;
* copie de données à partir du compte Stockage Blob Azure vers une base de données Azure SQL.

> [!NOTE]
> Les étapes présentées ici ont été adaptées du tutoriel plus détaillé fourni par l’équipe ADF : [Copier des données depuis une base de données SQL Server locale vers un stockage Blob Azure](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) fait référence aux sections appropriées de cette rubrique sont fournies si nécessaire.
>
>

## <a name="prereqs"></a>Configuration requise
Ce didacticiel part du principe que vous disposez de :

* Un **abonnement Azure**. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Un **compte de stockage Azure**. Dans ce didacticiel, vous utilisez un compte de stockage Azure pour stocker des données. Si vous ne possédez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../../storage/common/storage-account-create.md) . Après avoir créé le compte de stockage, vous devez obtenir la clé du compte utilisée pour accéder au stockage. Consultez [Gérer les clés d’accès au compte de stockage](../../storage/common/storage-account-keys-manage.md).
* Un accès à une **base de données Azure SQL Database**. Si vous devez configurer une base de données Azure SQL, l’article [Bien démarrer avec Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) fournit des informations sur le provisionnement d’une nouvelle instance Azure SQL Database.
* **Azure PowerShell** installé et configuré localement. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Cette procédure utilise le [portail Azure](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Téléchargement des données sur votre SQL Server local
Nous utilisons le [jeu de données NYC Taxi](https://chriswhong.com/open-data/foil_nyc_taxi/) pour illustrer le processus de migration. Le jeu de données NYC Taxi est disponible, comme mentionné dans cet article, sur Azure Blob Storage [données NYC Taxi](https://www.andresmh.com/nyctaxitrips/). Les données comprennent deux fichiers : le fichier trip_data.csv qui contient les détails de voyage et le fichier trip_far.csv qui contient les détails des prix payés pour chaque voyage. Un échantillon et une description de ces fichiers sont fournis dans la [description du jeu de données des voyages NYC Taxi](sql-walkthrough.md#dataset).

Vous pouvez adapter les procédures fournies ici à un jeu de vos propres données ou suivre les étapes décrites à l'aide du jeu de données NYC Taxi. Pour télécharger le jeu de données NYC Taxi dans votre base de données SQL Server locale, suivez la procédure décrite dans [Importer des données en bloc dans SQL Server Database](sql-walkthrough.md#dbload). Ces instructions concernent un SQL Server sur une machine virtuelle Azure, mais la procédure de téléchargement vers le serveur local SQL Server est la même.

## <a name="create-adf"></a> Création d’une Azure Data Factory
Les instructions pour la création d’une fabrique de données Azure Data Factory et d’un groupe de ressources dans le [portail Azure](https://portal.azure.com/) sont fournies dans [Créer une fabrique de données Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Nommez la nouvelle instance ADF *adfdsp* et nommez le groupe de ressources créé *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Installer et configurer Azure Data Factory Integration Runtime
Le runtime d’intégration est une infrastructure d’intégration de données managées client utilisée par Azure Data Factory pour fournir des capacités d’intégration de données entre différents environnements réseau. Ce runtime s’appelait auparavant « Data Management Gateway ».

Pour le configurer, [suivez les instructions de création d’un pipeline](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>Création de services liés pour la connexion aux ressources de données
Un service lié définit les informations nécessaires à Azure Data Factory pour se connecter à des ressources de données. Dans ce scénario, nous avons trois ressources pour lesquelles les services liés sont nécessaires :

1. SQL Server local
2. Stockage Blob Azure
3. Base de données Azure SQL

La procédure pas à pas pour la création de services liés est fournie dans [Créer des services liés](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="adf-tables"></a>Définir et créer des tables pour spécifier l’accès aux jeux de données
Créez des tables qui spécifient la structure, l'emplacement et la disponibilité des jeux de données avec les procédures reposant sur des scripts suivantes. Les fichiers JSON sont utilisés pour définir les tables. Pour plus d'informations sur la structure de ces fichiers, consultez [Jeux de données](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Vous devez exécuter l’applet de commande `Add-AzureAccount` avant d’exécuter l’applet de commande [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx), afin de vérifier que l’abonnement Azure approprié est sélectionné pour l’exécution de la commande. Pour obtenir la documentation de cette applet de commande, consultez [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

Les définitions reposant sur JSON dans les tables utilisent les noms suivants :

* Le **nom de table** sur le serveur SQL local est *nyctaxi_data*
* the **nom de conteneur** dans le compte de stockage d’objets blob Azure est *containername*

Trois définitions de table sont nécessaires pour ce pipeline ADF :

1. [Table SQL locale](#adf-table-onprem-sql)
2. [Table d’objets blob](#adf-table-blob-store)
3. [Table SQL Azure](#adf-table-azure-sql)

> [!NOTE]
> Ces procédures utilisent Azure PowerShell pour définir et créer les activités ADF. Toutefois, ces tâches peuvent également être accomplies à l’aide du portail Azure. Pour plus d’informations, consultez [Créer des jeux de données](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>Table SQL locale
La définition de table pour le SQL Server local est spécifié dans le fichier JSON suivant :

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Les noms de colonnes n’étaient pas inclus ici. Vous pouvez sélectionner des noms de colonnes en les incluant ici (pour plus d’informations, consultez la rubrique [documentation ADF](../../data-factory/copy-activity-overview.md) ).

Copiez la définition JSON de la table dans un fichier appelé *onpremtabledef.json* et enregistrez-le dans un emplacement connu (ici supposé comme étant *C:\temp\onpremtabledef.json*). Créez la table dans ADF avec la cmdlet Azure PowerShell suivante :

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Table d'objets blob
La définition de la table pour l’emplacement d’objets blob de sortie est la suivante (cela mappe les données ingérées localement vers un objet blob Azure) :

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Copiez la définition JSON de la table dans un fichier appelé *bloboutputtabledef.json* et enregistrez-le dans un emplacement connu (ici supposé comme étant *C:\temp\bloboutputtabledef.json*). Créez la table dans ADF avec la cmdlet Azure PowerShell suivante :

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>Table SQL Azure
La définition de la table pour la sortie SQL Azure est la suivante (ce schéma mappe les données provenant de l'objet blob) :

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Copiez la définition JSON de la table dans un fichier appelé *AzureSqlTable.json* et enregistrez-le dans un emplacement connu (ici supposé comme étant *C:\temp\AzureSqlTable.json*). Créez la table dans ADF avec la cmdlet Azure PowerShell suivante :

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>Définir et créer le pipeline
Spécifiez les activités appartenant au pipeline et créez le pipeline avec les procédures reposant sur des scripts suivantes. Un fichier JSON est utilisé pour définir les propriétés du pipeline.

* Le script suppose que le **nom du pipeline** est *AMLDSProcessPipeline*.
* Notez également que nous avons défini la périodicité du pipeline sur une exécution quotidienne et avec un temps d'exécution par défaut pour la tâche (12 h 00 UTC).

> [!NOTE]
> Les procédures suivantes utilisent Azure PowerShell pour définir et créer le pipeline ADF. Toutefois, cette tâche peut également être accomplie à l’aide du portail Azure. Pour plus d’informations, consultez [Création d’un pipeline](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

En utilisant les définitions de table fournies précédemment, la définition de pipeline pour ADF est spécifiée comme suit :

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-premises SQL server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Copiez cette définition JSON du pipeline dans un fichier appelé *pipelinedef.json* et enregistrez-le dans un emplacement connu (ici supposé comme étant *C:\temp\pipelinedef.json*). Créez le pipeline dans ADF avec l’applet de commande Azure PowerShell suivante :

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Lancer le pipeline
Le pipeline peut maintenant être exécuté à l'aide de la commande suivante :

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Les valeurs de paramètres *startdate* et *enddate* doivent être remplacées par les dates entre lesquelles le pipeline doit s’exécuter.

Une fois que le pipeline s'exécute, vous devez être en mesure de voir des données apparaître dans le conteneur sélectionné pour l'objet blob, à compter d’un fichier par jour.

Notez que nous n'avons pas tiré parti de la fonctionnalité fournie par ADF de canaliser les données de manière incrémentielle. Pour plus d’informations sur son utilisation et d’autres fonctionnalités fournies par ADF, consultez la [documentation ADF](https://azure.microsoft.com/services/data-factory/).
