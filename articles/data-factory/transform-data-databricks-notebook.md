---
title: Transformer des données avec Databricks Notebook
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment traiter ou transformer des données en exécutant un notebook Databricks dans Azure Data Factory.
ms.service: data-factory
ms.subservice: tutorials
ms.custom: synapse
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: d4db58e70c31724bac3678eb0f692e13d26211c6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122641325"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformer des données en exécutant un bloc-notes Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L’activité Azure Databricks Notebook dans un [pipeline Data Factory](concepts-pipelines-activities.md) exécute un bloc-notes Databricks dans votre espace de travail Azure Databricks. Cet article s'appuie sur l'article [Activités de transformation des données](transform-data.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.  Azure Databricks est une plateforme gérée pour exécuter Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Définition de l’activité Databricks Notebook

Voici l’exemple de définition JSON d’une activité Databricks Notebook :

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Propriétés de l’activité Databricks Notebook

Le tableau suivant décrit les propriétés JSON utilisées dans la définition JSON :

|Propriété|Description|Obligatoire|
|---|---|---|
|name|Nom de l'activité dans le pipeline.|Oui|
|description|Texte décrivant l’activité.|Non|
|type|Pour l’activité Databricks Notebook, le type d’activité est DatabricksNotebook.|Oui|
|linkedServiceName|Nom du service lié Databricks sur lequel s’exécute le bloc-notes Databricks. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md).|Oui|
|notebookPath|Chemin absolu du notebook à exécuter dans l’espace de travail Databricks. Ce chemin doit commencer par une barre oblique.|Oui|
|baseParameters|Tableau de paires clé-valeur. Des paramètres de base peuvent être utilisés pour chaque exécution d’activité. Si le notebook accepte un paramètre qui n’est pas spécifié, la valeur par défaut du notebook est utilisée. Pour obtenir d’autres paramètres, consultez [Databricks Notebooks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Non|
|libraries|Liste de bibliothèques à installer sur le cluster qui exécute le travail. Il peut s’agir d’un tableau de \<string, object>.|Non|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliothèques prises en charge pour les activités Databricks

Dans la définition d’activité Databricks ci-dessus, vous précisez ces types de bibliothèques : *jar*, *egg*, *whl*, *maven*, *pypi*, *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Pour plus d’informations, consultez la [documentation Databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) pour les types de bibliothèques.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Transmission de paramètres entre des notebooks et une fabrique de données

Vous pouvez transmettre les paramètres d'une fabrique de données à des notebooks en utilisant la propriété *baseParameters* dans l'activité Databricks.

Dans certains cas, il peut être nécessaire de retransmettre certaines valeurs du notebook vers la fabrique de données. Celles-ci peuvent être utilisées pour le flux de contrôle (contrôles conditionnels) dans la fabrique de données ou être consommées par les activités situées en aval (taille maximale autorisée : 2 Mo).

1. Dans votre notebook, vous pouvez appeler [dbutils.notebook.exit ("returnValue")](/azure/databricks/notebooks/notebook-workflows#notebook-workflows-exit) et la valeur « returnValue » correspondante sera renvoyée à la fabrique de données.

2. Vous pouvez utiliser la sortie dans la fabrique de données en utilisant une expression telle que `@{activity('databricks notebook activity name').output.runOutput}`. 

   > [!IMPORTANT]
   > Si vous transmettez un objet JSON, vous pouvez récupérer des valeurs en ajoutant des noms de propriété. Exemple : `@{activity('databricks notebook activity name').output.runOutput.PropertyName}`

## <a name="how-to-upload-a-library-in-databricks"></a>Comment charger une bibliothèque dans Databricks

### <a name="you-can-use-the-workspace-ui"></a>Vous pouvez utiliser l’interface utilisateur de l’espace de travail :

1. [Utiliser l’interface utilisateur de l’espace de travail Databricks](/azure/databricks/libraries/#create-a-library)

2. Pour obtenir le chemin dbfs de la bibliothèque ajoutée par le biais de l’interface utilisateur, vous pouvez utiliser l’[interface CLI de Databricks](/azure/databricks/dev-tools/cli/#install-the-cli).

   En général, les bibliothèques Jar sont stockées sous dbfs:/FileStore/jars lors de l’utilisation de l’interface utilisateur. Vous pouvez répertorier toutes les répertorier à l’aide de l’interface CLI : *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Vous pouvez utiliser l’interface CLI de Databricks :

1. Suivez [Copier la bibliothèque avec l’interface CLI de Databricks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs).

2. Utilisez l’interface CLI de Databricks [(étapes d’installation)](/azure/databricks/dev-tools/cli/#install-the-cli).

   Par exemple, pour copier un fichier JAR sur dbfs : `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
