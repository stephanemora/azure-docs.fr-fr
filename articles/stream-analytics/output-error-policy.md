---
title: Stratégies d’erreur de sortie dans Azure Stream Analytics
description: Découvrez plus d’informations sur les stratégies de gestion des erreurs de sortie disponibles dans Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
manager: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: f854e8ce35ac9d0a99b4a7dc1cdc66724114a5c4
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392566"
---
# <a name="output-error-policy"></a>Stratégie d’erreur de sortie
Cet article décrit les stratégies de gestion des erreurs de sortie qui peuvent être configurées dans Azure Stream Analytics.

Les stratégies de gestion des erreurs de sortie s’appliquent seulement aux erreurs de conversion de données qui se produisent quand l’événement de sortie généré par une tâche Stream Analytics n’est pas conforme au schéma du récepteur cible. Vous pouvez configurer cette stratégie en choisissant **Recommencer** ou **Supprimer**. Dans le portail Azure, quand vous êtes dans une tâche Stream Analytics, sous **Configurer**, sélectionnez **Stratégie d’erreur** pour effectuer votre sélection.

![Stratégie d’erreur - Emplacement](./media/stream-analytics-error-policy/stream-analytics-error-policy-locate.PNG)


## <a name="retry"></a>Recommencer
Quand une erreur se produit, Azure Stream Analytics effectue indéfiniment de nouvelles tentatives d’écriture de l’événement jusqu’à la réussite de l’écriture. Il n’existe pas de délai d’expiration pour les nouvelles tentatives. Au final, le traitement de tous les événements suivants est bloqué par l’événement qui fait l’objet des nouvelles tentatives. Cette option est la stratégie de gestion des erreurs de sortie par défaut.

## <a name="drop"></a>Supprimer
Azure Stream Analytics supprime tous les événements de sortie qui entraînent une erreur de conversion de données. Les événements supprimés ne peuvent pas être récupérés pour un retraitement ultérieur.


Toutes les erreurs temporaires (par exemple les erreurs de réseau) font l’objet de nouvelles tentatives, quelle que soit la configuration de la stratégie de gestion des erreurs de sortie.


## <a name="next-steps"></a>Étapes suivantes
[Guide de dépannage pour Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
