---
title: Transformer des données avec Databricks Jar
description: Découvrez comment traiter ou transformer des données en exécutant une activité Databricks Jar dans un pipeline Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: ccfe8fbf330e1c7f6f415b64a1f18d93a084a0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374012"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformer des données en exécutant une activité Jar dans Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L’activité Azure Databricks Jar dans un [pipeline Data Factory](concepts-pipelines-activities.md) exécute un fichier Spark Jar dans votre cluster Azure Databricks. Cet article s'appuie sur l'article [Activités de transformation des données](transform-data.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.  Azure Databricks est une plateforme gérée pour exécuter Apache Spark.

Pour une présentation de onze minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Définition de l’activité Databricks Jar

Voici l’exemple de définition JSON d’une activité Databricks Jar :

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Propriétés de l’activité Databricks Jar

Le tableau suivant décrit les propriétés JSON utilisées dans la définition JSON :

|Propriété|Description|Obligatoire|
|:--|---|:-:|
|name|Nom de l'activité dans le pipeline.|Oui|
|description|Texte décrivant l’activité.|Non|
|type|Pour l’activité Databricks Jar, le type d’activité est DatabricksSparkJar.|Oui|
|linkedServiceName|Nom du service lié Databricks sur lequel s’exécute l’activité Jar. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md).|Oui|
|mainClassName|Nom complet de la classe contenant la méthode principale à exécuter. Cette classe doit être contenue dans un fichier JAR fourni en tant que bibliothèque. Un fichier JAR peut contenir plusieurs classes. Chacune des classes peut contenir une méthode main.|Oui|
|parameters|Paramètres qui sont transmis à la méthode principale. Cette propriété est un tableau de chaînes.|Non|
|libraries|Liste de bibliothèques à installer sur le cluster qui exécute le travail. Il peut s’agir d’un tableau de < chaîne, objet >|Oui (au moins une contenant la méthode mainClassName)|

> [!NOTE]
> **Problème connu** : quand vous utilisez le même [cluster interactif](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) pour exécuter simultanément plusieurs activités Databricks Jar (sans redémarrage du cluster), vous pouvez rencontrer un problème connu avec Databricks. En effet, dans ce cas, les paramètres de la première activité sont également utilisés par les activités suivantes. Des paramètres incorrects sont alors transmis aux travaux suivants. Pour limiter ce risque, utilisez plutôt un [cluster de travail](compute-linked-services.md#example---using-new-job-cluster-in-databricks).

## <a name="supported-libraries-for-databricks-activities"></a>Bibliothèques prises en charge pour les activités Databricks

Dans la définition d’activité Databricks précédente, vous avez spécifié ces types de bibliothèque : `jar`, `egg`, `maven`, `pypi`, `cran`.

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
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Pour plus d’informations, consultez la [documentation Databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) pour les types de bibliothèques.

## <a name="how-to-upload-a-library-in-databricks"></a>Comment charger une bibliothèque dans Databricks

### <a name="you-can-use-the-workspace-ui"></a>Vous pouvez utiliser l’interface utilisateur de l’espace de travail :

1. [Utiliser l’interface utilisateur de l’espace de travail Databricks](/azure/databricks/libraries/#create-a-library)

2. Pour obtenir le chemin dbfs de la bibliothèque ajoutée par le biais de l’interface utilisateur, vous pouvez utiliser l’[interface CLI de Databricks](/azure/databricks/dev-tools/cli/#install-the-cli).

   En général, les bibliothèques Jar sont stockées sous dbfs:/FileStore/jars lors de l’utilisation de l’interface utilisateur. Vous pouvez répertorier toutes les répertorier à l’aide de l’interface CLI : *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Vous pouvez utiliser l’interface CLI de Databricks :

1. Suivez [Copier la bibliothèque avec l’interface CLI de Databricks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs).

2. Utilisez l’interface CLI de Databricks [(étapes d’installation)](/azure/databricks/dev-tools/cli/#install-the-cli).

   Par exemple, pour copier un fichier JAR sur dbfs : `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Étapes suivantes

Pour une présentation de onze minutes et la démonstration de cette fonctionnalité, regardez cette [vidéo](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player).
