---
title: Variables système dans Azure Data Factory
description: Cet article décrit les variables système prises en charge par Azure Data Factory. Vous pouvez utiliser ces variables dans des expressions lors de la définition des entités Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 119ecb3ec9c208340f09f513bf10b3ad24312cb5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201224"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variables système prises en charge par Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit les variables système prises en charge par Azure Data Factory. Vous pouvez utiliser ces variables dans des expressions lors de la définition des entités Data Factory.

## <a name="pipeline-scope"></a>Étendue du pipeline

Ces variables système peuvent être référencées n’importe où dans le code JSON de pipeline.

| Nom de la variable | Description |
| --- | --- |
| @pipeline().DataFactory |Nom de la fabrique de données dans laquelle le pipeline s’exécute. |
| @pipeline().Pipeline |Nom du pipeline |
| @pipeline().RunId |ID de l’exécution du pipeline spécifique |
| @pipeline().TriggerType |Type du déclencheur qui a appelé le pipeline (par exemple `ScheduleTrigger` ou `BlobEventsTrigger`). Pour connaître la liste des types de déclencheurs pris en charge, consultez [Exécution de pipeline et déclencheurs dans Azure Data Factory](concepts-pipeline-execution-triggers.md). Le type de déclencheur `Manual` indique que le pipeline a été déclenché manuellement. |
| @pipeline().TriggerId|ID du déclencheur qui a appelé le pipeline. |
| @pipeline().TriggerName|Nom du déclencheur qui a appelé le pipeline. |
| @pipeline().TriggerTime|Heure de l’exécution du déclencheur qui a appelé le pipeline. Il s’agit de l’heure à laquelle le déclencheur s’est **réellement** déclenché pour appeler l’exécution du pipeline. Elle peut différer légèrement de l’heure planifiée du déclencheur.  |

>[!NOTE]
>Les variables système de date/heure liées aux déclencheurs (dans la portée du pipeline ou dans celle du déclencheur) retournent des dates UTC au format ISO 8601, par exemple `2017-06-01T22:20:00.4061448Z`.

## <a name="schedule-trigger-scope"></a>Étendue de déclencheur de planification

Il est possible de faire référence à ces variables système n’importe où dans les déclencheurs JSON de type [ScheduleTrigger](concepts-pipeline-execution-triggers.md#schedule-trigger).

| Nom de la variable | Description |
| --- | --- |
| @trigger().scheduledTime |Heure à laquelle le déclencheur a été planifié pour appeler l’exécution du pipeline. |
| @trigger().startTime |Heure à laquelle le déclencheur s’est **réellement** déclenché pour appeler l’exécution du pipeline. Elle peut différer légèrement de l’heure planifiée du déclencheur. |

## <a name="tumbling-window-trigger-scope"></a>Étendue de déclencheur de fenêtre bascule

Il est possible de faire référence à ces variables système n’importe où dans les déclencheurs JSON de type [TumblingWindowTrigger](concepts-pipeline-execution-triggers.md#tumbling-window-trigger).

| Nom de la variable | Description |
| --- | --- |
| @trigger().outputs.windowStartTime |Début de la fenêtre associée à l’exécution du déclencheur. |
| @trigger().outputs.windowEndTime |Fin de la fenêtre associée à l’exécution du déclencheur. |
| @trigger().scheduledTime |Heure à laquelle le déclencheur a été planifié pour appeler l’exécution du pipeline. |
| @trigger().startTime |Heure à laquelle le déclencheur s’est **réellement** déclenché pour appeler l’exécution du pipeline. Elle peut différer légèrement de l’heure planifiée du déclencheur. |

## <a name="storage-event-trigger-scope"></a>Étendue du déclencheur d’événements de stockage

Il est possible de faire référence à ces variables système n’importe où dans les déclencheurs JSON de type [BlobEventsTrigger](concepts-pipeline-execution-triggers.md#event-based-trigger).

| Nom de la variable | Description |
| --- | --- |
| @triggerBody().fileName  |Nom du fichier dont la création ou la suppression a entraîné le déclenchement du déclencheur.   |
| @triggerBody().folderName  |Chemin du dossier contenant le fichier spécifié par `@triggerBody().fileName`. Le premier segment du chemin du dossier correspond au nom du conteneur de Stockage Blob Azure.  |
| @trigger().startTime |Heure à laquelle le déclencheur s’est déclenché pour appeler l’exécution du pipeline. |

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’utilisation de ces variables dans les expressions, consultez [Langage et fonctions des expressions](control-flow-expression-language-functions.md).
* Pour utiliser les variables système de l’étendue du déclencheur dans le pipeline, consultez [Référencer les métadonnées de déclencheur dans le pipeline](how-to-use-trigger-parameterization.md)
