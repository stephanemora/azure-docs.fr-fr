---
title: Comment démarrer un travail Azure Stream Analytique
description: Cet article décrit comment démarrer un travail d’Analytique de Stream.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 9bc3e4132919e5fc5baadc78841e66efd3c34bcd
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005945"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Comment démarrer un travail Azure Stream Analytique

Vous pouvez démarrer votre travail Azure Stream Analytique à l’aide du portail Azure, Visual Studio et PowerShell. Lorsque vous démarrez un travail, vous sélectionnez une heure pour le travail commencer à créer la sortie. Portail Azure, Visual Studio et PowerShell ont différentes méthodes pour définir l’heure de début. Ces méthodes sont décrites ci-dessous.

## <a name="start-options"></a>Options de démarrage
Les trois options suivantes sont disponibles pour démarrer un travail. Notez que toutes les heures mentionnées ci-dessous sont celles spécifiées dans [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Si TIMESTAMP BY n’est pas spécifié, l’heure d’arrivée sera utilisé.
* **Maintenant**: Rend le point de départ de l’événement de sortie identique à diffuser lorsque le travail est démarré. Si un opérateur temporels est utilisé (par exemple, fenêtre de temps, un retard ou jointure), Azure Stream Analytique sera automatiquement repense les données dans la source d’entrée. Par exemple, si vous démarrez un travail de « Maintenant » et si votre requête utilise une fenêtre bascule de 5 minutes, Azure Stream Analytique recherche des données à partir de 5 minutes auparavant dans l’entrée.
Le premier événement de sortie possibles aurait un horodatage égal à ou supérieur à l’heure actuelle, et ASA garantit que tous les événements d’entrée qui peuvent logiquement contribuent à la sortie a été pris en compte. Par exemple, aucune fonction d’agrégation fenêtrées partielle n’est générées. Il est toujours la valeur agrégée terminée.

* **Personnalisé** : Vous pouvez choisir le point de départ de la sortie. Similaire à la **maintenant** option, Azure Stream Analytique lit automatiquement les données avant cette heure si un opérateur temporels est utilisé. 

* **Lors du dernier arrêt**. Cette option est disponible lorsque le travail a été démarré précédemment, mais a été arrêté manuellement ou a échoué. Lorsque vous choisissez cette option, Azure Stream Analytique utilisera la dernière heure de sortie pour redémarrer le travail, par conséquent, aucune donnée n’est perdue. De même pour les options précédentes, Azure Stream Analytique automatiquement lit les données avant cette heure si un opérateur temporels est utilisé. Étant donné que plusieurs partitions d’entrée peuvent avoir différents temps, la première heure d’arrêt de toutes les partitions est utilisée, ainsi que des doublons peuvent se produire dans la sortie. Plus d’informations sur exactement-une fois le traitement sont disponibles sur la page [garanties de remise d’événement](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Portail Azure

Accédez à votre travail dans le portail Azure et sélectionnez **Démarrer** sur la page de présentation. Sélectionnez un **heure de début de sortie de la tâche** , puis sélectionnez **Démarrer**.

Choisissez une des options pour **heure de début de sortie de la tâche**. Les options sont *maintenant*, *personnalisé*, et, si le travail a été exécuté précédemment, *lors du dernier arrêt*. Voir ci-dessus pour plus d’informations sur ces options.

## <a name="visual-studio"></a>Visual Studio

Dans la vue des travaux, sélectionnez le bouton fléché vert pour démarrer le travail. Définir le **Mode de démarrage de sortie de travail** et sélectionnez **Démarrer**. L’état du travail devient **en cours d’exécution**.

Il existe trois options pour **Mode de démarrage de sortie de travail**: *JobStartTime*, *CustomTime*, et *LastOutputEventTime*. Si cette propriété est absente, la valeur par défaut est *JobStartTime*. Voir ci-dessus pour plus d’informations sur ces options.


## <a name="powershell"></a>PowerShell

Utilisez l’applet de commande suivante pour démarrer votre travail à l’aide de PowerShell :

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Il existe trois options pour **propriété OutputStartMode**: *JobStartTime*, *CustomTime*, et *LastOutputEventTime*. Si cette propriété est absente, la valeur par défaut est *JobStartTime*. Voir ci-dessus pour plus d’informations sur ces options.

Pour plus d’informations sur la `Start-AzStreamAnalyitcsJob` applet de commande, afficher le [Start-AzStreamAnalyticsJob référence](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer un travail Stream Analytique à l’aide du portail Azure](stream-analytics-quick-create-portal.md)
* [Démarrage rapide : Créer un travail Stream Analytics à l’aide d’Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Démarrage rapide : Créer un travail Stream Analytics à l’aide d’Azure Stream Analytics Tools pour Visual Studio](stream-analytics-quick-create-vs.md)
