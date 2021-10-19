---
title: Supervision d’Azure Database pour MySQL – serveur flexible avec classeurs Azure Monitor
description: Décrit le fonctionnement de la supervision d’Azure Database pour MySQL – serveur flexible avec classeurs Azure Monitor.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: a773048b2d1ddf8ad7b7993ef7975517506bbd07
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621085"
---
# <a name="monitoring-azure-database-for-mysql---flexible-server-with-azure-monitor-workbooks"></a>Supervision d’Azure Database pour MySQL – serveur flexible avec classeurs Azure Monitor

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database pour MySQL – serveur flexible désormais intégré dans les classeurs Azure Monitor. Les classeurs fournissent un canevas flexible pour l’analyse des données et la création de rapports visuels enrichis au sein du portail Azure. Ils vous permettent d’exploiter plusieurs sources de données à travers l’écosystème Azure et de les combiner dans des expériences interactives unifiées. Les classeurs servent de rapports organisés qui sont conçus pour une réutilisation flexible par plusieurs utilisateurs et équipes. L’ouverture d’un modèle crée un classeur temporaire rempli avec le contenu du modèle. Avec cette intégration, le serveur dispose d’un lien vers les classeurs et de quelques exemples de modèles, qui permettent de superviser le service à grande échelle. Vous pouvez modifier ces modèles, les personnaliser selon les besoins du client et les épingler au tableau de bord pour créer une vue ciblée et organisée des ressources Azure.
 
Dans cet article, vous allez découvrir les différents modèles de classeurs disponibles pour votre serveur flexible

Trois modèles par défaut sont disponibles pour Azure Database pour MySQL – serveur flexible
 
- **Vue d’ensemble :** fournit le résumé de l’instance et les métriques avancées pour mieux comprendre l’utilisation des ressources sur votre serveur. Vous pourrez voir les informations ci-dessous concernant Azure Database pour MySQL – serveur flexible

    * Résumé de serveur 
    * Résumé de la base de données
    * Métriques de connexion 
    * Mesures de performances 
    * Métriques de stockage 

* **Audit :** résumé et détails des événements d’audit collectés pour le serveur. Les vues suivantes sont disponibles avec ce modèle pour Azure Database pour MySQL – serveur flexible

    * Actions administratives sur le service
    * Résumé de l’audit
    * Résumé de l’audit des événements de connexion
    * Audit des événements de connexion
    * Résumé de l’accès aux tables
    * Erreurs identifiées

* **Query Performance Insights :** résumé et détails de la charge de travail de requête sur l’instance, requête longue, analyse de requête lente et métriques de connexion. La vue suivante est disponible avec ce modèle pour la base de données Azure pour MySQL – serveur flexible.

    * Chargement de requête
    * Nombre total de connexions actives
    * Tendance des requêtes lentes (durée des requêtes supérieure à 10 secondes)
    * Détails des requêtes lentes
    * Répertorier les cinq requêtes les plus longues
    * Résumer les requêtes lentes selon le temps de requête minimal, maximal, moyen et d’écart type

Vous pouvez également modifier ces modèles et les personnaliser en fonction de vos besoins. Pour plus d’informations, consultez [Vue d’ensemble des classeurs Azure Monitor – Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)

 ## <a name="how-to-access-workbook-templates"></a>Comment accéder aux modèles de classeurs

Pour afficher les modèles, sur le portail Azure, accédez au panneau **Supervision** pour Azure Database pour MySQL – Serveur flexible et sélectionnez **Classeurs**.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="Diagramme montrant les classeurs.":::

Vous pouvez également consulter la liste des modèles en accédant aux **modèles publics**.

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="Diagramme montrant des modèles de classeurs":::


## <a name="next-steps"></a>Étapes suivantes
- [Classeurs Azure Monitor](../../azure-monitor/visualize/workbooks-access-control.md) et découvrez-en plus sur les nombreuses options pour les visualisations enrichies des classeurs.
- [Démarrage avec les classeurs Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#visualizations) et découvrez les nombreuses options de visualisations enrichies des classeurs.
