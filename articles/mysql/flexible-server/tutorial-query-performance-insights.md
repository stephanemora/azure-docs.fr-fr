---
title: 'Tutoriel : Query Performance Insight pour Serveur flexible Azure Database pour MySQL'
description: Cet article vous présente les outils à utiliser pour mieux visualiser Query Performance Insight pour Serveur flexible Azure Database pour MySQL.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: 63cec8fa00af3e4711c4c6383c68cc09d32ce3d3
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065708"
---
# <a name="tutorial-query-performance-insight-for-azure-database-for-mysql-flexible-server"></a>Tutoriel : Query Performance Insight pour Serveur flexible Azure Database pour MySQL
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Query Performance Insight se propose de fournir une analyse intelligente des requêtes pour les bases de données. Les insights de prédilection sont les modèles de charge de travail et les requêtes de longue durée. La compréhension de ces insights peut vous aider à identifier les requêtes à optimiser pour améliorer les performances globales et utiliser efficacement vos ressources disponibles. 

Query Performance Insight est conçu pour vous permettre de passer moins de temps à résoudre les problèmes de performances de base de données en fournissant les informations suivantes :
* *N* premières requêtes de longue durée et leurs tendances associées.
* Détails des requêtes : Consultez le texte de la requête ainsi que l’historique de l’exécution indiquant le temps de requête minimal, maximal, moyen et d’écart type.
* Utilisation des ressources (processeur, mémoire et stockage).
 
Cet article explique comment utiliser les journaux des requêtes lentes MySQL, l’outil Log Analytics et les modèles de classeurs pour visualiser Query Performance Insight pour Serveur flexible Azure Database pour MySQL.

Ce didacticiel vous montre comment effectuer les opérations suivantes :
>[!div class="checklist"]
> * Configurer des journaux des requêtes lentes à l’aide du portail Azure ou d’Azure CLI
> * Configurer les diagnostics
> * Afficher des journaux des requêtes lentes avec Log Analytics 
> * Afficher des journaux des requêtes lentes avec des classeurs 

## <a name="prerequisites"></a>Prérequis

- [Créez une instance de Serveur flexible Azure Database pour MySQL](./quickstart-create-server-portal.md).
- [Créez un espace de travail Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).


## <a name="configure-slow-query-logs-by-using-the-azure-portal"></a>Configurer des journaux des requêtes lentes à l’aide du portail Azure 

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez votre instance de serveur flexible.

1. Dans le volet gauche, sous **Paramètres**, sélectionnez **Paramètres du serveur**.

   :::image type="content" source="./media//tutorial-query-performance-insights/server-parameters.png" alt-text="Capture d’écran montrant la liste « Paramètres du serveur ».":::

1. Pour le paramètre **slow_query_log**, sélectionnez **ON**.

   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query-log-enable.png" alt-text="Capture d’écran montrant le paramètre « slow_query_log » en position « ON ».":::

1. Pour les autres paramètres, comme **long_query_time** et **log_slow_admin_statements**, reportez-vous à la documentation sur les [journaux des requêtes lentes](./concepts-slow-query-logs.md#configure-slow-query-logging).  

   :::image type="content" source="./media/tutorial-query-performance-insights/long-query-time.png" alt-text="Capture d’écran montrant les valeurs mises à jour pour les paramètres liés aux journaux des requêtes lentes restants.":::

1. Sélectionnez **Enregistrer**. 

   :::image type="content" source="./media/tutorial-query-performance-insights/save-parameters.png" alt-text="Capture d’écran du bouton « Enregistrer » permettant d’enregistrer les modifications apportées aux valeurs des paramètres.":::

Vous pouvez revenir à la liste des journaux en fermant la page **Paramètres du serveur**.

## <a name="configure-slow-query-logs-by-using-the-azure-cli"></a>Configurer des journaux des requêtes lentes à l’aide d’Azure CLI
 
Vous pouvez aussi activer et configurer des journaux des requêtes lentes pour votre serveur flexible à partir d’Azure CLI en exécutant la commande suivante : 

> [!IMPORTANT]
> Pour veiller à ce que les performances du serveur flexible ne soient pas lourdement impactées, nous vous recommandons de journaliser uniquement les types d’événements et les utilisateurs nécessaires à des fins d’audit.

```azurecli
# Turn on statement level log.

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 seconds. This setting will log all queries that execute for more than 10 seconds. Adjust this threshold based on your definition for slow queries.

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable slow query logs.

az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="set-up-diagnostics"></a>Configurer les diagnostics

Les journaux des requêtes lentes sont intégrés aux paramètres de diagnostic Azure Monitor pour vous permettre de canaliser vos journaux vers l’un des trois récepteurs de données suivants :
* Un espace de travail Log Analytics
* Un hub d’événements
* Un compte de stockage

>[!Note]
>Vous devez créer vos récepteurs de données avant de configurer les paramètres de diagnostic. Vous pouvez accéder aux journaux des requêtes lentes dans les récepteurs de données que vous avez configurés. L’affichage des journaux peut prendre jusqu’à 10 minutes.

1. Dans le volet de gauche, sous **Supervision**, sélectionnez **Paramètres de diagnostic**.

1. Dans le volet **Paramètres de diagnostic**, sélectionnez **Ajouter un paramètre de diagnostic**.

   :::image type="content" source="./media/tutorial-query-performance-insights/add-diagnostic-setting.png" alt-text="Capture d’écran du lien « Ajouter un paramètre de diagnostic » dans le volet « Paramètres de diagnostic ».":::

1. Dans la zone **Nom**, entrez un nom pour le paramètre de diagnostic.

1. Spécifiez les destinations (espace de travail Log Analytics, hub d’événements ou compte de stockage) auxquelles envoyer les journaux des requêtes lentes en cochant leurs cases correspondantes.

    >[!Note]
    > Pour ce tutoriel, vous allez envoyer les journaux des requêtes lentes à un espace de travail Log Analytics.

1. Sous **Journal**, pour le type de journal, cochez la case **MySqlSlowLogs**.

    :::image type="content" source="./media/tutorial-query-performance-insights/configure-diagnostic-setting.png" alt-text="Capture d’écran du volet « Paramètres de diagnostic » permettant de sélectionner des options de configuration.":::

1. Après avoir configuré les récepteurs de données auxquels envoyer les journaux des requêtes lentes, sélectionnez **Enregistrer**.

    :::image type="content" source="./media/tutorial-query-performance-insights/save-diagnostic-setting.png" alt-text="Capture d’écran des options de configuration des paramètres de diagnostic, avec l’option Enregistrer mise en évidence":::

## <a name="view-query-insights-by-using-log-analytics"></a>Afficher les insights de requête en utilisant Log Analytics 

1. Dans Log Analytics, dans le volet gauche, sous **Supervision**, sélectionnez **Journaux**.

1. Fermez la fenêtre **Requêtes** qui s’ouvre.

   :::image type="content" source="./media/tutorial-query-performance-insights/log-query.png" alt-text="Capture d’écran du volet « Requêtes » de Log Analytics.":::

1. Dans la fenêtre de requête, vous pouvez écrire la requête à exécuter. Pour trouver les requêtes qui durent plus de 10 secondes sur un serveur particulier, nous avons utilisé le code suivant :

   ```kusto
   AzureDiagnostics
      | where Category == 'MySqlSlowLogs'
      | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
      | where query_time_d > 10
   ```
    
1. Sélectionnez l’**intervalle de temps**, puis exécutez la requête. Les résultats apparaissent dans l’image suivante :  

   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query.png" alt-text="Capture d’écran d’un journal des requêtes lentes.":::

## <a name="view-query-insights-by-using-workbooks"></a>Afficher les insights de requête avec des classeurs 

1. Dans le portail Azure, dans le volet gauche, sous **Supervision** pour votre instance de Serveur flexible Azure Database pour MySQL, sélectionnez **Classeurs**.

1.  Sélectionnez le modèle **Query Performance Insight**. 

    :::image type="content" source="./media/tutorial-query-performance-insights/monitor-workbooks.png" alt-text="Capture d’écran montrant tous les classeurs dans la galerie de classeurs.":::

Dans le classeur, vous pouvez voir les visualisations suivantes : 
>[!div class="checklist"]
> * Chargement de requête
> * Nombre total de connexions actives
> * Tendance des requêtes lentes (durée des requêtes supérieure à 10 secondes)
> * Détails des requêtes lentes
> * Lister les 5 requêtes les plus longues
> * Résumer les requêtes lentes selon le temps de requête minimal, maximal, moyen et d’écart type

    
:::image type="content" source="./media/tutorial-query-performance-insights/long-query.png" alt-text="Capture d’écran montrant deux requêtes longues.":::

>[!Note]
> * Pour voir l’utilisation des ressources, vous pouvez utiliser le modèle Vue d’ensemble.
> * Vous pouvez aussi modifier ces modèles et les personnaliser selon vos besoins. Pour plus d’informations, consultez la [vue d’ensemble des classeurs Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).
> * Pour une consultation rapide, vous pouvez aussi épingler les classeurs ou la requête Log Analytics sur votre tableau de bord. Pour plus d’informations, consultez [Créer un tableau de bord dans le portail Azure](../../azure-portal/azure-portal-dashboards.md). 

Dans Query Performance Insight, deux métriques peuvent vous aider à trouver les goulots d’étranglement potentiels : la *durée* et le *nombre d’exécutions*. Les requêtes de longue durée ont le plus grand risque de verrouiller des ressources le plus longtemps, de bloquer d’autres utilisateurs et de limiter l’évolutivité. 

Dans certains cas, un nombre d’exécutions élevé peut augmenter le nombre d’allers-retours réseau. Les allers-retours réseau affectent les performances. Ils peuvent entraîner une latence du réseau et une latence du serveur en aval. Le nombre d’exécutions peut donc faciliter la recherche des requêtes fréquemment exécutées (« bavardes »). Ces requêtes peuvent être facilement optimisées. 

## <a name="next-steps"></a>Étapes suivantes
- [Découvrez-en plus sur les classeurs Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#visualizations) et leurs options de visualisation enrichies.
- [Découvrez-en plus sur les journaux des requêtes lentes](concepts-slow-query-logs.md).


