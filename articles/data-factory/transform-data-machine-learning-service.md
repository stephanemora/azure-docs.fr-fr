---
title: Exécuter des pipelines Azure Machine Learning service dans des pipelines Azure Data Factory | Microsoft Docs
description: Découvrez comment exécuter des pipelines Azure Machine Learning service dans des pipelines Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: ddaa020b3567f8f00cfd7fc7f7728f1160d9306b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72302022"
---
# <a name="execute-azure-machine-learning-service-pipelines-in-azure-data-factory"></a>Exécuter des pipelines Azure Machine Learning service dans Azure Data Factory

Exécutez des pipelines Azure Machine Learning service en tant qu’étape dans des pipelines Azure Data Factory. L’activité d’exécution de pipeline Machine Learning permet des scénarios de prédiction par lot tels que l’identification des possibles non-remboursements de prêt, la détermination des sentiments et l’analyse des comportements des clients.

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
Nom | Nom de l’activité dans le pipeline | Chaîne | OUI
Type | Le type d’activité est « AzureMLExecutePipeline ». | Chaîne | OUI
linkedServiceName | Service lié à Azure Machine Learning service | référence de service lié | OUI
mlPipelineId | ID du pipeline Azure Machine Learning publié | Chaîne (ou expression avec resultType de chaîne) | OUI
experimentName | Nom de l’expérience de l’historique des exécutions pour l’exécution du pipeline Machine Learning | Chaîne (ou expression avec resultType de chaîne) | Non
mlPipelineParameters | Paires clé/valeur à passer au point de terminaison du pipeline Azure Machine Learning publié. Les clés doivent correspondre aux noms des paramètres de pipeline définis dans le pipeline Machine Learning publié. | Objet avec des paires clé/valeur (ou expression avec l’objet resultType) | Non
mlParentRunId | ID d’exécution du pipeline Azure Machine Learning service parent | Chaîne (ou expression avec resultType de chaîne) | Non
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
