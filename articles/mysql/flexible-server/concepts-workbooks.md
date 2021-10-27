---
title: Superviser le serveur flexible Azure Database pour MySQL à l’aide de workbooks Azure Monitor
description: Cet article décrit comment vous pouvez superviser le serveur flexible Azure Database pour MySQL à l’aide de workbooks Azure Monitor.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: aa26531dd9f644b4dc5b3343674abd7920fe57dd
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130064587"
---
# <a name="monitor-azure-database-for-mysql-flexible-server-by-using-azure-monitor-workbooks"></a>Superviser le serveur flexible Azure Database pour MySQL à l’aide de workbooks Azure Monitor

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Le serveur flexible Azure Database pour MySQL est désormais intégré aux workbooks Azure Monitor. Les workbooks vous offrent un canevas flexible pour l’analyse des données et la création de rapports visuels enrichis au sein du portail Azure. Les workbooks vous permettent d’exploiter plusieurs sources de données au sein d’Azure et de les combiner dans des expériences interactives unifiées. Les modèles de workbooks servent de rapports organisés qui sont conçus pour une réutilisation flexible par plusieurs utilisateurs et équipes. 

Lorsque vous ouvrez un modèle, vous créez un workbook temporaire qui est rempli avec le contenu du modèle. Avec cette intégration, le serveur dispose d’un lien vers les workbooks et quelques exemples de modèles, ce qui peut vous aider à superviser le service à grande échelle. Vous pouvez modifier ces modèles, les personnaliser selon vos besoins et les épingler au tableau de bord pour créer une vue ciblée et organisée des ressources Azure.
 
Dans cet article, vous allez découvrir les différents modèles de workbooks disponibles pour votre serveur flexible.

Trois modèles sont disponibles pour le serveur flexible Azure Database pour MySQL :
 
- **Vue d’ensemble** : affiche le récapitulatif d’une instance et les métriques avancées pour vous aider à visualiser et à comprendre l’utilisation des ressources sur votre serveur. Ce modèle affiche les vues suivantes :

    * Résumé de serveur 
    * Résumé de la base de données
    * Métriques de connexion 
    * Mesures de performances 
    * Métriques de stockage 

* **Audit** : affiche un récapitulatif et des détails sur les événements d’audit collectés pour le serveur. Ce modèle affiche les vues suivantes :

    * Actions administratives sur le service
    * Résumé de l’audit
    * Résumé de l’audit des événements de connexion
    * Audit des événements de connexion
    * Résumé de l’accès aux tables
    * Erreurs identifiées

* **Query Performance Insight** : affiche un récapitulatif et des détails de la charge de travail de requête sur l’instance, requête longue, analyse de requête lente et métriques de connexion. Ce modèle affiche les vues suivantes :

    * Chargement de requête
    * Nombre total de connexions actives
    * Tendance des requêtes lentes (durée des requêtes supérieure à 10 secondes)
    * Détails des requêtes lentes
    * Lister les 5 requêtes les plus longues
    * Résumer les requêtes lentes selon le temps de requête minimal, maximal, moyen et d’écart type

Vous pouvez aussi modifier et personnaliser ces modèles selon vos besoins. Pour plus d’informations, consultez la [vue d’ensemble des workbooks Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).

 ## <a name="access-the-workbook-templates"></a>Accéder aux modèles de workbooks

Pour afficher les modèles dans le portail Azure, accédez au volet **Supervision** pour le serveur flexible Azure Database pour MySQL et sélectionnez **Workbooks**.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="Capture d’écran montrant les modèles « Vue d’ensemble », « Audit » et « Query Performance Insight » dans le volet Workbooks.":::

Vous pouvez également afficher la liste des modèles en accédant au volet **Modèles publics**.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="Diagramme montrant les modèles « Vue d’ensemble », « Audit » et « Query Performance Insight » dans le volet Modèles publics.":::


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur le [contrôle d’accès](../../azure-monitor/visualize/workbooks-access-control.md) dans les workbooks Azure Monitor.
- En savoir plus sur les [options de visualisation](../../azure-monitor/visualize/workbooks-overview.md#visualizations) dans les workbooks Azure Monitor. 
