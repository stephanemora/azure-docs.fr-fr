---
title: Événement de fin de redimensionnement de pool Azure Batch
description: Référence pour l’événement de fin de redimensionnement de pool Batch. Consultez un exemple de pool dont la taille a augmenté et qui s’est terminé avec succès.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: e2c66471ad9fe8d917d1ffddceb6e01c339d62dd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022220"
---
# <a name="pool-resize-complete-event"></a>Événement de fin de redimensionnement de pool

 Cet événement est émis quand un redimensionnement de pool est terminé ou a échoué.

 L’exemple suivant montre le corps d’un événement de fin de redimensionnement de pool pour un pool dont l’augmentation de la taille s’est terminée correctement.

```
{
    "id": "myPool",
    "nodeDeallocationOption": "invalid",
        "currentDedicatedNodes": 10,
        "targetDedicatedNodes": 10,
    "currentLowPriorityNodes": 5,
        "targetLowPriorityNodes": 5,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "resultCode": "Success",
    "resultMessage": "The operation succeeded"
}
```

|Élément|Type|Notes|
|-------------|----------|-----------|
|`id`|String|ID du pool.|
|`nodeDeallocationOption`|String|Spécifie quand des nœuds peuvent être supprimés du pool en cas de diminution de la taille du pool.<br /><br /> Les valeurs possibles sont les suivantes :<br /><br /> **requeue** : arrêter les tâches en cours d’exécution et les replacer en file d’attente. Les tâches sont ré-exécutées lors de l’activation du travail. Supprimez les nœuds dès que les tâches sont terminées.<br /><br /> **terminate** : mettre fin aux tâches en cours d’exécution. Les tâches ne sont pas ré-exécutées. Supprimez les nœuds dès que les tâches sont terminées.<br /><br /> **taskcompletion** : autoriser l’achèvement des tâches en cours d’exécution. Ne planifiez aucune nouvelle tâche en attendant. Supprimer les nœuds quand toutes les tâches sont terminées.<br /><br /> **Retaineddata** : autoriser l’achèvement des tâches en cours d’exécution, puis attendre que toutes les périodes de rétention des données expirent. Ne planifiez aucune nouvelle tâche en attendant. Supprimez les nœuds une fois que toutes les périodes de rétention ont expiré.<br /><br /> La valeur par défaut est requeue.<br /><br /> Si la taille du pool augmente, cela signifie que la valeur est définie **invalide**.|
|`currentDedicatedNodes`|Int32|Nombre de nœuds de calcul dédiés actuellement affectés au pool.|
|`targetDedicatedNodes`|Int32|Nombre de nœuds de calcul dédiés demandés pour le pool.|
|`currentLowPriorityNodes`|Int32|Nombre de nœuds de calcul de faible priorité actuellement affectés au pool.|
|`targetLowPriorityNodes`|Int32|Nombre de nœuds de calcul de faible priorité demandés pour le pool.|
|`enableAutoScale`|Bool|Spécifie si la taille du pool s’ajuste automatiquement au fil du temps.|
|`isAutoPool`|Bool|Spécifie si le pool a été créé via un mécanisme AutoPool du travail.|
|`startTime`|DateTime|Heure de début du redimensionnement du pool.|
|`endTime`|DateTime|Heure de fin du redimensionnement du pool.|
|`resultCode`|String|Résultat du redimensionnement.|
|`resultMessage`|String| Message détaillé sur le résultat.<br /><br /> Si le redimensionnement s’est terminé correctement, indique que l’opération a réussi.|
