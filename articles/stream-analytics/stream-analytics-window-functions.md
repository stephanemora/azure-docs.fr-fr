---
title: Présentation des fonctions de fenêtrage d’Azure Stream Analytics
description: Cet article décrit quatre fonctions de fenêtrage (bascules, récurrentes, glissantes, session) qui sont utilisées dans les travaux Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a0547243ddf114d5c9f7034f182a5e76d8c3e016
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369420"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Présentation des fonctions de fenêtrage de Stream Analytics

Dans les scénarios de streaming en temps réel, il est courant d’effectuer des opérations sur les données contenues dans des fenêtres temporelles. Stream Analytics a une prise en charge native des fonctions de fenêtrage, permettant aux développeurs de créer des travaux de traitement de flux complexes avec un minimum d’effort.

Quatre types de fenêtres temporelles sont disponibles : [**bascule**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [**récurrente**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [**glissante**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics) et [**session**](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics).  Vous utilisez les fonctions de fenêtre dans la clause [**GROUP BY**](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) de la syntaxe de requête dans vos travaux Stream Analytics. Vous pouvez également agréger des événements sur plusieurs fenêtres avec la fonction [**Windows()** ](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Toutes les opérations de [fenêtrage](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) renvoient les résultats à la **fin** de la fenêtre. La sortie de la fenêtre sera un événement unique qui dépendra de la fonction d’agrégation utilisée. L’événement de sortie aura l’horodatage de la fin de la fenêtre et toutes les fonctions de fenêtrage sont définies avec une longueur fixe. 

![Concepts des fonctions de fenêtrage de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Fenêtre bascule
Les fonctions de fenêtre bascule sont utilisées pour segmenter un flux de données en segments temporels distincts et exécuter une fonction sur ces segments, comme dans l’exemple ci-dessous. Les principales caractéristiques des fenêtres bascules sont les suivantes : elles se répètent, elles ne se chevauchent pas et un événement ne peut pas appartenir à plusieurs fenêtres bascules.

![Fenêtre bascule Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Fenêtre récurrente
Les fonctions de fenêtre récurrente font des bonds d’une durée fixe dans le temps. Il peut être facile de les considérer comme des fenêtres bascules pouvant se chevaucher : par conséquent, les événements peuvent appartenir au jeu de résultats de plusieurs fenêtres récurrentes. Pour créer une fenêtre récurrente identique à une fenêtre bascule, spécifiez une taille de bond égale à la taille de la fenêtre. 

![Fenêtre récurrente Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Fenêtre glissante
Les fonctions de fenêtre glissante, à la différence des fenêtres bascules ou récurrentes, ne génèrent une sortie **que** lorsqu’un événement se produit. Chaque fenêtre a au moins un événement et avance en permanence d’un € (epsilon). Comme pour les fenêtres récurrentes, les événements peuvent appartenir à plusieurs fenêtres glissantes.

![Fenêtre glissante Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Fenêtre session
Les fonctions de fenêtre session regroupent les événements qui arrivent au même moment, en filtrant les périodes de temps où il n’existe aucune donnée. Il existe trois paramètres principaux : délai d’expiration, durée maximale et clé de partitionnement (facultative).

![Fenêtre session Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

Une fenêtre de session commence lorsque le premier événement se produit. Si un autre événement se produit dans le délai d’expiration spécifié à partir du dernier événement réceptionné, alors la fenêtre s’étend pour inclure le nouvel événement. Dans le cas contraire, si aucun événement ne se produit dans le délai imparti, la fenêtre est fermée une fois le délai expiré.

Si des événements continuent à se produire durant le délai d’expiration spécifié, la fenêtre de session continuera à s’étendre jusqu'à ce que la durée maximale soit atteinte. Les intervalles de vérification de la durée maximale sont définis pour être de la même taille que la durée maximale spécifiée. Par exemple, si la durée maximale est égale à 10, alors les vérifications pour voir si la fenêtre dépasse la durée maximale se produisent à t = 0, 10, 20, 30, etc.

Lorsqu’une clé de partition est fournie, les événements sont regroupés par la clé et la fenêtre de session est appliquée indépendamment à chaque groupe. Ce partitionnement est utile lorsque vous avez besoin de fenêtres de session différentes pour différents utilisateurs ou appareils.


## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

