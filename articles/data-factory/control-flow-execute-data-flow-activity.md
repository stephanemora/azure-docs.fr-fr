---
title: Activité d’exécution de flux de données dans Azure Data Factory | Microsoft Docs
description: Comment exécuter des flux de données à l’intérieur d’un pipeline de fabrique de données.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 24b27c16573a35b1d8749d7ff381fbef970f4bd0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471657"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Activité d’exécution de flux de données dans Azure Data Factory
Utilisez l’activité d’exécution de flux de données pour exécuter votre flux de données ADF dans les exécutions de débogage de pipeline (bac à sable) et dans les exécutions de pipeline déclenchées.

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

* ```dataflow``` correspond au nom de l’entité de flux de données que vous souhaitez exécuter
* ```compute``` décrit l’environnement d’exécution Spark
* ```coreCount``` est le nombre de cœurs à affecter à l’exécution de cette activité de flux de données

![Exécuter le flux de données](media/data-flow/activity-data-flow.png "Exécuter le flux de données")

### <a name="debugging-pipelines-with-data-flows"></a>Débogage des pipelines avec des flux de données

![Bouton de débogage](media/data-flow/debugbutton.png "Bouton de débogage")

Utilisez le débogage des flux de données pour utiliser un cluster préparé afin de tester vos flux de données de façon interactive dans une exécution de débogage de pipeline. Utilisez l’option de débogage de pipeline pour tester votre flux de données à l’intérieur d’un pipeline.

### <a name="run-on"></a>Exécuter sur

Il s’agit d’un champ obligatoire qui définit le runtime d’intégration à utiliser pour l’exécution de votre activité de flux de données. Par défaut, Data Factory utilise le runtime d’intégration Azure avec résolution automatique par défaut. Toutefois, vous pouvez créer vos propres runtimes d’intégration Azure, ce qui vous permet de définir des régions spécifiques, le type de calcul, le nombre de cœurs et de durée de vie de l’exécution de votre activité de flux de données.

Le paramètre par défaut pour les exécutions de flux de données est de 8 cœurs de calcul général avec une durée de vie de 60 minutes.

Choisissez l’environnement de calcul pour cette exécution de votre flux de données. La valeur par défaut est le runtime d’intégration Azure avec résolution automatique par défaut. Ce choix exécute le flux de données sur l’environnement Spark dans la même région que votre fabrique de données. Le type de calcul est un cluster de travail, ce qui signifie que l’environnement de calcul peut prendre plusieurs minutes à démarrer.

Vous avez le contrôle sur l’environnement d’exécution Spark pour vos activités de flux de données. Dans le [runtime d’intégration Azure](concepts-integration-runtime.md), vous trouverez des paramètres permettant de définir le type de calcul (usage général, mémoire optimisée et calcul optimisé), le nombre de cœurs de worker et la durée de vie, pour aligner le moteur d’exécution avec les exigences de calcul de votre flux de données. En outre, définir une durée de vie vous permettra de maintenir un cluster préparé, immédiatement disponible pour les exécutions de travaux.

![Runtime d’intégration Azure](media/data-flow/ir-new.png "Runtime d’intégration Azure")

> [!NOTE]
> La sélection du runtime d’intégration dans l’activité de flux de données s’applique uniquement aux *exécutions déclenchées* de votre pipeline. Le débogage de votre pipeline avec l’activité de flux de données avec débogage s’exécute sur le cluster Spark à 8 cœurs par défaut.

### <a name="staging-area"></a>Zone de transit

Si vos données sont reçues dans Azure Data Warehouse, vous devez choisir un emplacement intermédiaire pour le chargement par lots Polybase. Les paramètres de transit sont uniquement applicables aux charges de travail Azure Data Warehouse.

## <a name="parameterized-datasets"></a>Jeux de données paramétrables

Si vous utilisez des jeux de données paramétrables, veillez à définir les valeurs des paramètres.

![Paramètres d’exécution des flux de données](media/data-flow/params.png "Paramètres")

## <a name="parameterized-data-flows"></a>Flux de données paramétrables

Si votre flux de données contient des paramètres, définissez les valeurs dynamiques de vos paramètres de flux de données dans la section Paramètres de l’activité d’exécution de flux de données. Vous pouvez utiliser le langage d’expression de pipeline ADF (uniquement pour les types de paramètres de chaîne) ou le langage d’expression de flux de données pour définir les valeurs des paramètres avec des expressions dynamiques ou des valeurs littérales statiques.

![Exemple de paramètre d’exécution de flux de données](media/data-flow/parameter-example.png "Exemple de paramètre")

### <a name="debugging-data-flows-with-parameters"></a>Débogage des flux de données avec des paramètres

À l’heure actuelle, vous pouvez uniquement déboguer les flux de données avec des paramètres à l’aide de l’exécution de débogage de pipeline de l’activité d’exécution de flux de données. Les sessions de débogage interactives dans le flux de données ADF seront bientôt disponibles. Les exécutions de pipeline et exécutions de débogage, toutefois, fonctionnent avec des paramètres.

Une bonne pratique consiste à créer votre flux de données avec du contenu statique afin que la propagation complète des colonnes de métadonnées soit disponible au moment de la conception pour le dépannage. Remplacez ensuite le jeu de données statique avec un jeu de données paramétrable dynamique lors de la mise en fonctionnement de votre pipeline de flux de données.

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory : 

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
