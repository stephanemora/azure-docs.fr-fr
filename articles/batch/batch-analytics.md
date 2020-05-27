---
title: Analytique Azure Batch
description: Les rubriques relatives à l’analyse en mode batch contiennent des informations de référence concernant les événements et alertes disponibles pour les ressources de service Batch.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: b07d362c3cc2d3b086cf0a82325025604907a393
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726857"
---
# <a name="batch-analytics"></a>Analyse en mode batch
Les rubriques relatives à l’analyse en mode batch contiennent des informations de référence concernant les événements et alertes disponibles pour les ressources de service Batch.

Pour plus d’informations sur l’activation et l’utilisation des journaux de diagnostic de Batch, voir [Journalisation des diagnostics Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/).

## <a name="diagnostic-logs"></a>Journaux de diagnostic

Le service Microsoft Azure Batch émet les événements de journal de diagnostic suivants pendant la durée de vie de certaines ressources Batch.

**Événements du journal de service**
* [Création de pool](batch-pool-create-event.md)
* [Début de suppression de pool](batch-pool-delete-start-event.md)
* [Fin de suppression de pool](batch-pool-delete-complete-event.md)
* [Début de redimensionnement de pool](batch-pool-resize-start-event.md)
* [Fin de redimensionnement de pool](batch-pool-resize-complete-event.md)
* [Début de tâche](batch-task-start-event.md)
* [Fin de tâche](batch-task-complete-event.md)
* [Échec de tâche](batch-task-fail-event.md)