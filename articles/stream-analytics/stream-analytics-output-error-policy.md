---
title: Stratégies d’erreur de sortie dans Azure Stream Analytics
description: Découvrez plus d’informations sur les stratégies de gestion des erreurs de sortie disponibles dans Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/30/2021
ms.custom: seodec18
ms.openlocfilehash: 1fdd973f51ee564bf59a7ed4fac347a007836d52
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110794049"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Stratégies d’erreur de sortie d’Azure Stream Analytics
Cet article décrit les stratégies de gestion des erreurs de sortie qui peuvent être configurées dans Azure Stream Analytics.

Les stratégies de gestion des erreurs de sortie s’appliquent seulement aux erreurs de conversion de données qui se produisent quand l’événement de sortie généré par une tâche Stream Analytics n’est pas conforme au schéma du récepteur cible. Vous pouvez configurer cette stratégie en choisissant **Recommencer** ou **Supprimer**. Dans le portail Azure, quand vous êtes dans une tâche Stream Analytics, sous **Configurer**, sélectionnez **Stratégie d’erreur** pour effectuer votre sélection.

![Emplacement des stratégies d’erreur de sortie d’Azure Stream Analytics](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Recommencer
Quand une erreur se produit, Azure Stream Analytics effectue indéfiniment de nouvelles tentatives d’écriture de l’événement jusqu’à la réussite de l’écriture. Il n’existe pas de délai d’expiration pour les nouvelles tentatives. Au final, le traitement de tous les événements suivants est bloqué par l’événement qui fait l’objet des nouvelles tentatives. Cette option est la stratégie de gestion des erreurs de sortie par défaut. L’intervalle avant nouvelle tentative peut varier en fonction de chaque sortie et peut aller de quelques secondes à quelques minutes au cours des nouvelles tentatives suivantes.

## <a name="drop"></a>Supprimer
Azure Stream Analytics supprime tous les événements de sortie qui entraînent une erreur de conversion de données. Les événements supprimés ne peuvent pas être récupérés pour un retraitement ultérieur.


Toutes les erreurs temporaires (par exemple les erreurs de réseau) font l’objet de nouvelles tentatives, quelle que soit la configuration de la stratégie de gestion des erreurs de sortie.


## <a name="next-steps"></a>Étapes suivantes
[Guide de dépannage pour Azure Stream Analytics](./stream-analytics-troubleshoot-query.md)
