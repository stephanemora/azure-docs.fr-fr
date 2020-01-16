---
title: Azure Batch Analytics | Microsoft Docs
description: Référence pour Azure Batch Analytics.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 39a8bfb6a48bf55ae9f2ec36f7716959e6ada9dd
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027388"
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