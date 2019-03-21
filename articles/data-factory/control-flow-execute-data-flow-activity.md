---
title: Exécution d’activité de flux de données dans Azure Data Factory | Microsoft Docs
description: L’activité de flux de données execute exécute des flux de données.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 856f4bd9c2b04ff10ed598c5e641955e1de99398
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792410"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Exécution d’activité de flux de données dans Azure Data Factory
Utiliser l’activité de flux de données execute pour exécuter votre flux de données ADF dans les exécutions de pipeline debug (sandbox) et dans les exécutions de pipeline déclenchée.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Propriétés type

* ```dataflow``` est le nom de l’entité de flux de données que vous souhaitez exécuter
* ```compute``` Décrit l’environnement d’exécution de Spark
* ```coreCount``` est le nombre de cœurs à affecter à cette exécution de l’activité de votre flux de données

![Exécuter le flux de données](media/data-flow/activity-data-flow.png "exécuter le flux de données")

### <a name="run-on"></a>Exécuter sur

Choisissez l’environnement de calcul pour cette exécution de votre flux de données. La valeur par défaut est le Runtime d’intégration Azure-résolution automatique par défaut. Ce choix s’exécute le flux de données sur l’environnement Spark dans la même région que votre fabrique de données. Le type de calcul sera un cluster de travail, ce qui signifie que l’environnement de calcul peut prendre plusieurs minutes pour le démarrage.

### <a name="debugging-pipelines-with-data-flows"></a>Débogage des pipelines avec des flux de données

![Bouton de débogage](media/data-flow/debugbutton.png "le bouton de débogage")

Utilisez les données flux de débogage d’utiliser un cluster chauffé pour tester votre flux de données de façon interactive dans une exécution de débogage de pipeline. Utilisez l’option de débogage de pipeline pour tester votre flux de données à l’intérieur d’un pipeline.

### <a name="compute-type"></a>Type de calcul

Vous pouvez choisir à usage général, de calcul optimisé ou à mémoire optimisée, selon les besoins de votre flux de données.

### <a name="core-count"></a>Nombre de cœurs

Choisissez le nombre de cœurs à affecter au travail. Pour les petits travaux, moins de cœurs fonctionne mieux.

### <a name="staging-area"></a>Zone de transit

Si vous sont réception vos données dans Azure Data Warehouse, vous devez choisir un emplacement intermédiaire de votre charge de traitement par lots de Polybase.

## <a name="parameterized-datasets"></a>Jeux de données paramétrables

Si vous utilisez des jeux de données paramétrables, veillez à définir les valeurs de paramètre.

![Exécuter les paramètres de flux de données](media/data-flow/params.png "paramètres")

### <a name="debugging-parameterized-data-flows"></a>Débogage des flux de données paramétrables

Vous pouvez uniquement déboguer les flux de données avec des jeux de données paramétrables à partir de la déboguer de Pipeline exécuté à l’aide de l’activité de flux de données execute. Actuellement, les sessions de débogage interactif dans le flux de données ADF ne fonctionnent pas avec les jeux de données paramétrables. Exécutions de pipeline et les exécutions de débogage fonctionnent avec des paramètres.

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
