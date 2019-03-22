---
title: Comment démarrer un travail Azure Stream Analytique
description: Cet article décrit comment démarrer un travail d’Analytique de Stream.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fb1d724907c09e2eb77930f5a235336ca8cd3a25
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886845"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Comment démarrer un travail Azure Stream Analytique

Vous pouvez démarrer votre travail Azure Stream Analytique à l’aide du portail Azure, Visual Studio et PowerShell. Lorsque vous démarrez un travail, vous sélectionnez une heure pour le travail commencer à créer la sortie. Portail Azure, Visual Studio et PowerShell ont différentes méthodes pour définir l’heure de début. Ces méthodes sont décrites ci-dessous.

## <a name="azure-portal"></a>Portail Azure

Accédez à votre travail dans le portail Azure et sélectionnez **Démarrer** sur la page de présentation. Sélectionnez un **heure de début de sortie de la tâche** , puis sélectionnez **Démarrer**.

Il existe trois options pour **heure de début de sortie de la tâche**: *Maintenant*, *personnalisé*, et *lors du dernier arrêt*. En sélectionnant *maintenant* démarre la tâche à l’heure actuelle. En sélectionnant *personnalisé* vous permet de définir un temps personnalisé dans le passé ou le futur pour le début du travail. Pour reprendre une tâche arrêtée sans perdre de données, choisissez *lors du dernier arrêt*.

## <a name="visual-studio"></a>Visual Studio

Dans la vue des travaux, sélectionnez le bouton fléché vert pour démarrer le travail. Définir le **Mode de démarrage de sortie de travail** et sélectionnez **Démarrer**. L’état du travail devient **en cours d’exécution**.

Il existe trois options pour **Mode de démarrage de sortie de travail**: *JobStartTime*, *CustomTime*, et *LastOutputEventTime*. Si cette propriété est absente, la valeur par défaut est *JobStartTime*.

*JobStartTime* rend le point de départ de l’événement de sortie de diffuser le même que lorsque le travail est démarré.

*CustomTime* démarre la sortie à la fois personnalisée qui est spécifié dans le *OutputStartTime* paramètre.

*LastOutputEventTime* rend le point de départ l’événement du flux de sortie le même que le dernier événement de sortie de temps.

## <a name="powershell"></a>PowerShell

Utilisez l’applet de commande suivante pour démarrer votre travail à l’aide de PowerShell :

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Il existe trois options pour **propriété OutputStartMode**: *JobStartTime*, *CustomTime*, et *LastOutputEventTime*. Si cette propriété est absente, la valeur par défaut est *JobStartTime*.

*JobStartTime* rend le point de départ de l’événement de sortie de diffuser le même que lorsque le travail est démarré.

*CustomTime* démarre la sortie à la fois personnalisée qui est spécifié dans le *OutputStartTime* paramètre.

*LastOutputEventTime* rend le point de départ l’événement du flux de sortie le même que le dernier événement de sortie de temps.

Pour plus d’informations sur la `Start-AzStreamAnalyitcsJob` applet de commande, afficher le [Start-AzStreamAnalyticsJob référence](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer un travail Stream Analytics à l’aide du portail Azure](stream-analytics-quick-create-portal.md)
* [Démarrage rapide : Créer un travail Stream Analytique à l’aide d’Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Démarrage rapide : créer une tâche Stream Analytics à l’aide des outils Azure Stream Analytics pour Visual Studio](stream-analytics-quick-create-vs.md)
