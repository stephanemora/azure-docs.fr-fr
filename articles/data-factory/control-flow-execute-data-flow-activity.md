---
title: Exécution d’activité de flux de données dans Azure Data Factory | Microsoft Docs
description: Comment exécuter les données passe à l’intérieur d’un pipeline data factory.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: c33219eacb1d3bada5630a7792f98ba33dba824e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235858"
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

### <a name="debugging-pipelines-with-data-flows"></a>Débogage des pipelines avec des flux de données

![Bouton de débogage](media/data-flow/debugbutton.png "le bouton de débogage")

Utilisez les données flux de débogage d’utiliser un cluster chauffé pour tester votre flux de données de façon interactive dans une exécution de débogage de pipeline. Utilisez l’option de débogage de Pipeline pour tester votre flux de données à l’intérieur d’un pipeline.

### <a name="run-on"></a>Exécuter sur

Il s’agit d’un champ obligatoire qui définit le Runtime d’intégration à utiliser pour votre exécution de l’activité de flux de données. Par défaut, Data Factory utilise le runtime d’intégration d’Azure de résoudre automatiquement par défaut. Toutefois, vous pouvez créer vos propres Runtimes d’intégration Azure qui définissent des régions spécifiques, type, de cœurs et de durée de vie de calcul pour l’exécution de l’activité de votre flux données.

Le paramètre par défaut pour les exécutions de flux de données est de 8 cœurs de calcul général avec une durée de vie de 60 minutes.

Choisissez l’environnement de calcul pour cette exécution de votre flux de données. La valeur par défaut est le Runtime d’intégration Azure-résolution automatique par défaut. Ce choix s’exécute le flux de données sur l’environnement Spark dans la même région que votre fabrique de données. Le type de calcul sera un cluster de travail, ce qui signifie que l’environnement de calcul peut prendre plusieurs minutes pour le démarrage.

Vous contrôlez l’environnement d’exécution de Spark pour vos activités de flux de données. Dans le [runtime d’intégration Azure](concepts-integration-runtime.md) sont des paramètres pour définir le type de calcul (usage général, mémoire optimisée et optimisé pour le calcul), nombre de cœurs de travail et time-to-live pour faire correspondre le moteur d’exécution avec votre calcul de flux de données configuration requise. En outre, définissant la durée de vie vous permettra de maintenir un cluster à chaud est immédiatement disponible pour les exécutions de tâches.

![Runtime d’intégration Azure](media/data-flow/ir-new.png "Runtime d’intégration Azure")

> [!NOTE]
> La sélection de Runtime d’intégration dans l’activité de flux de données s’applique uniquement aux *déclenchée exécutions* de votre pipeline. Débogage de votre pipeline avec le flux de données avec le débogage s’exécute sur un cluster Spark par défaut de 8 cœurs.

### <a name="staging-area"></a>Zone de transit

Si vous sont réception vos données dans Azure Data Warehouse, vous devez choisir un emplacement intermédiaire de votre charge de traitement par lots de Polybase.

## <a name="parameterized-datasets"></a>Jeux de données paramétrables

Si vous utilisez des jeux de données paramétrables, veillez à définir les valeurs de paramètre.

![Exécuter les paramètres de flux de données](media/data-flow/params.png "paramètres")

### <a name="debugging-parameterized-data-flows"></a>Débogage des flux de données paramétrables

Vous pouvez uniquement déboguer les flux de données avec des jeux de données paramétrables à partir de la déboguer de Pipeline exécuté à l’aide de l’activité de flux de données execute. Actuellement, les sessions de débogage interactif dans le flux de données ADF ne fonctionnent pas avec les jeux de données paramétrables. Exécutions de pipeline et les exécutions de débogage fonctionnent avec des paramètres.

Une bonne pratique consiste à créer votre flux de données avec un jeu de données statique afin que vous ayez disponible de la propagation de la colonne de métadonnées complètes au moment du design. Remplacez ensuite le jeu de données statique avec un jeu de données paramétrable dynamique lorsque vous rendre opérationnel votre pipeline de flux de données.

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
