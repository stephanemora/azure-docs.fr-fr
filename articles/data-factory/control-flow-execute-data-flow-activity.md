---
title: Activité de flux de données dans Azure Data Factory | Microsoft Docs
description: Comment exécuter des flux de données à l’intérieur d’un pipeline de fabrique de données.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.author: makromer
ms.date: 10/07/2019
ms.openlocfilehash: cbfa1acac34187263f8c4203e41bbe61d7e4c745
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030513"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Activité de flux de données dans Azure Data Factory

Utilisez l’activité de flux de données pour transformer et déplacer des données par le biais des flux de données de mappage. Si vous débutez avec les flux de données, consultez [Vue d’ensemble des flux de données de mappage](concepts-data-flow-overview.md)

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
dataflow | Référence au flux de données en cours d’exécution | DataFlowReference | OUI
integrationRuntime | Environnement de calcul sur lequel le flux de données s’exécute | IntegrationRuntimeReference | OUI
staging.linkedService | Si vous utilisez une source ou un récepteur SQL DW, le compte de stockage utilisé pour la préproduction de PolyBase | LinkedServiceReference | Uniquement si le flux de données lit ou écrit dans un entrepôt de données SQL DW
staging.folderPath | Si vous utilisez une source ou un récepteur SQL DW, chemin du dossier dans le compte de stockage blob utilisé pour la préproduction de PolyBase | Chaîne | Uniquement si le flux de données lit ou écrit dans un entrepôt de données SQL DW

![Exécuter le flux de données](media/data-flow/activity-data-flow.png "Exécuter le flux de données")

### <a name="data-flow-integration-runtime"></a>Runtime d’intégration de flux de données

Choisissez le runtime d’intégration à utiliser pour l’exécution de votre activité de flux de données. Par défaut, Data Factory utilise le runtime d’intégration Azure à résolution automatique avec quatre cœurs worker et aucune durée de vie (TTL). Ce runtime d’intégration a un type de calcul à usage général et s’exécute dans la même région que votre fabrique. Vous pouvez créer votre propre runtime d’intégration Azure qui définit des régions spécifiques, un type de calcul, un nombre de cœurs et une durée de vie pour l’exécution de votre activité de flux de données.

Pour les exécutions de pipeline, le cluster est un cluster de travail, dont le démarrage prend quelques minutes avant que l’exécution commence. Si aucune durée de vie n’est spécifiée, ce temps de démarrage est nécessaire sur chaque exécution de pipeline. Si vous spécifiez une durée de vie, un pool de clusters à chaud reste actif pendant la durée spécifiée après la dernière exécution, ce qui donne lieu à des temps de démarrage plus courts. Par exemple, si vous avez une durée de vie de 60 minutes et que vous exécutez un flux de données une fois par heure, le pool de clusters reste actif. Pour plus d’informations, consultez [Runtime d’intégration Azure](concepts-integration-runtime.md).

![Runtime d’intégration Azure](media/data-flow/ir-new.png "Runtime d’intégration Azure")

> [!NOTE]
> La sélection du runtime d’intégration dans l’activité de flux de données s’applique uniquement aux *exécutions déclenchées* de votre pipeline. Le débogage de votre pipeline avec des flux de données s’exécute sur le cluster spécifié dans la session de débogage.

### <a name="polybase"></a>PolyBase

Si vous utilisez Azure SQL Data Warehouse comme récepteur ou source, vous devez choisir un emplacement de préproduction pour le chargement par lots PolyBase. PolyBase permet d’effectuer des chargements par lots en bloc au lieu de charger les données ligne par ligne. PolyBase réduit considérablement le temps de chargement dans l’instance SQL DW.

## <a name="parameterizing-data-flows"></a>Paramétrage de flux de données

### <a name="parameterized-datasets"></a>Jeux de données paramétrables

Si votre flux de données utilise des jeux de données paramétrables, définissez les valeurs de paramètre sous l’onglet **Paramètres**.

![Paramètres d’exécution des flux de données](media/data-flow/params.png "Paramètres")

### <a name="parameterized-data-flows"></a>Flux de données paramétrables

Si votre flux de données est paramétré, définissez les valeurs dynamiques de ses paramètres sous l’onglet **Paramètres**. Vous pouvez utiliser le langage d’expression de pipeline ADF (uniquement pour les types Chaîne) ou le langage d’expression de flux de données pour affecter des valeurs de paramètres littérales ou statiques. Pour plus d’informations, consultez [Paramètres de flux de données](parameters-data-flow.md).

![Exemple de paramètre d’exécution de flux de données](media/data-flow/parameter-example.png "Exemple de paramètre")

## <a name="pipeline-debug-of-data-flow-activity"></a>Débogage de pipeline de l’activité de flux de données

Pour effectuer une exécution de débogage de pipeline à l’aide d’une activité de flux de données, vous devez passer en mode de débogage de flux de données à l’aide du curseur **Débogage de flux de données** dans la barre supérieure. Le mode débogage vous permet d’exécuter le flux de données sur un cluster Spark actif. Pour plus d’informations, consultez [Mode de débogage](concepts-data-flow-debug-mode.md).

![Bouton de débogage](media/data-flow/debugbutton.png "Bouton de débogage")

Le pipeline de débogage s’exécute sur le cluster de débogage actif, et non sur l’environnement de runtime d’intégration spécifié dans les paramètres d’activité de flux de données. Vous pouvez choisir l’environnement de calcul de débogage lors du démarrage du mode de débogage.

## <a name="monitoring-the-data-flow-activity"></a>Supervision de l’activité de flux de données

L’activité de flux de données offre une expérience de supervision spéciale dans laquelle vous pouvez voir des informations relatives au partitionnement, au temps de phase et à la traçabilité des données. Ouvrez le volet de supervision à l’aide de l’icône de lunettes sous **Actions**. Pour plus d’informations, consultez [Supervision des flux de données](concepts-data-flow-monitoring.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez les activités de flux de contrôle prises en charge par Data Factory : 

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
