---
title: Exécuter des pipelines Azure Machine Learning
description: Découvrez comment exécuter des pipelines Azure Machine Learning dans des pipelines Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418998"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Exécuter des pipelines Azure Machine Learning dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Exécutez des pipelines Azure Machine Learning en tant qu’étape dans des pipelines Azure Data Factory. L’activité d’exécution de pipeline Machine Learning permet des scénarios de prédiction par lot tels que l’identification des possibles non-remboursements de prêt, la détermination des sentiments et l’analyse des comportements des clients.

La vidéo ci-dessous est une présentation de six minutes et la démonstration de cette fonctionnalité.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
name | Nom de l’activité dans le pipeline | String | Oui
type | Le type d’activité est « AzureMLExecutePipeline ». | String | Oui
linkedServiceName | Service lié à Azure Machine Learning | référence de service lié | Oui
mlPipelineId | ID du pipeline Azure Machine Learning publié | Chaîne (ou expression avec resultType de chaîne) | Oui
experimentName | Nom de l’expérience de l’historique des exécutions pour l’exécution du pipeline Machine Learning | Chaîne (ou expression avec resultType de chaîne) | Non
mlPipelineParameters | Paires clé/valeur à passer au point de terminaison du pipeline Azure Machine Learning publié. Les clés doivent correspondre aux noms des paramètres de pipeline définis dans le pipeline Machine Learning publié. | Objet avec des paires clé/valeur (ou expression avec l’objet resultType) | Non
mlParentRunId | ID d’exécution du pipeline Azure Machine Learning parent | Chaîne (ou expression avec resultType de chaîne) | Non
continueOnStepFailure | Indique s’il faut poursuivre l’exécution des autres étapes lors de l’exécution du pipeline Machine Learning en cas d’échec d’une étape. | boolean | Non

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants qui expliquent comment transformer des données par d’autres moyens :

* [Exécuter une activité de flux de données](control-flow-execute-data-flow-activity.md)
* [Activité U-SQL](transform-data-using-data-lake-analytics.md)
* [Activité Hive](transform-data-using-hadoop-hive.md)
* [Activité pig](transform-data-using-hadoop-pig.md)
* [Activité MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Activité de diffusion en continu Hadoop](transform-data-using-hadoop-streaming.md)
* [Activité Spark](transform-data-using-spark.md)
* [Activité personnalisée .NET](transform-data-using-dotnet-custom-activity.md)
* [Activité de procédure stockée](transform-data-using-stored-procedure.md)
