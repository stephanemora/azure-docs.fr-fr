---
title: Transformer des données avec Databricks Notebook - Azure | Microsoft Docs
description: Découvrez comment traiter ou transformer des données en exécutant un bloc-notes Databricks.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: 5f21f33678b8cf09d9dbd8966d42b1a5ebac9ffb
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224650"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformer des données en exécutant un bloc-notes Databricks

L’activité Azure Databricks Notebook dans un [pipeline Data Factory](concepts-pipelines-activities.md) exécute un bloc-notes Databricks dans votre espace de travail Azure Databricks. Cet article s'appuie sur l'article [Activités de transformation des données](transform-data.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge. Azure Databricks est une plateforme gérée pour exécuter Apache Spark.

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
|Nom|Nom de l'activité dans le pipeline.|Oui|
|description|Texte décrivant l’activité.|Non |
|Type|Pour l’activité Databricks Notebook, le type d’activité est DatabricksNotebook.|Oui|
|linkedServiceName|Nom du service lié Databricks sur lequel s’exécute le bloc-notes Databricks. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md).|Oui|
|notebookPath|Chemin absolu du notebook à exécuter dans l’espace de travail Databricks. Ce chemin doit commencer par une barre oblique.|Oui|
|baseParameters|Tableau de paires clé-valeur. Des paramètres de base peuvent être utilisés pour chaque exécution d’activité. Si le notebook accepte un paramètre qui n’est pas spécifié, la valeur par défaut du notebook est utilisée. Pour obtenir d’autres paramètres, consultez [Databricks Notebooks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Non |
|libraries|Liste de bibliothèques à installer sur le cluster qui exécute le travail. Il peut s’agir d’un tableau de \<chaîne, objet>.|Non |


## <a name="supported-libraries-for-databricks-activities"></a>Bibliothèques prises en charge pour les activités Databricks

Dans la définition d’activité Databricks ci-dessus, vous précisez ces types de bibliothèques : *jar*, *egg*, *maven*, *pypi*,  *cran*.

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
                "repo": "http://cran.us.r-project.org"
            }
        }
    ]
}

```

Pour plus d’informations, consultez la [documentation Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) pour les types de bibliothèques.

## <a name="how-to-upload-a-library-in-databricks"></a>Comment charger une bibliothèque dans Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Interface utilisateur de l’espace de travail Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Pour obtenir le chemin dbfs de la bibliothèque ajoutée par le biais de l’interface utilisateur, vous pouvez utiliser [CLI Databricks (installation)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

En général, les bibliothèques Jar sont stockées sous dbfs:/FileStore/jars lors de l’utilisation de l’interface utilisateur. Vous pouvez toutes les répertorier à l’aide de l’interface de ligne de commande : *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copier une bibliothèque avec CLI Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemple : *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
