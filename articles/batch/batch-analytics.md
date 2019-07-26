---
title: Azure Batch Analytics | Microsoft Docs
description: Référence pour Azure Batch Analytics.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 0f8233846f3835a2bbccf9a7072f223d435b90af
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323829"
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