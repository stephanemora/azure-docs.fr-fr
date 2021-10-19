---
title: 'Tutoriel : Query Performance Insight pour Azure Database pour MySQL - Serveur flexible'
description: 'Tutoriel : Query Performance Insight pour Azure Database pour MySQL - Serveur flexible'
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: ed78f493021c94c8beeecb8d5470d9a846b19d8d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621347"
---
# <a name="tutorial-query-performance-insights-for-azure-database-for-mysql--flexible-server"></a>Tutoriel : Query Performance Insight pour Azure Database pour MySQL - Serveur flexible
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Query Performance Insight se propose de fournir une analyse intelligente des requêtes pour les bases de données. Les insights favoris sont les modèles de charge de travail et les requêtes de longue durée. Cette analyse facilite la recherche des requêtes à optimiser en vue d’améliorer les performances globales et d’utiliser efficacement les ressources disponibles. Query Performance Insight devrait vous aider à résoudre plus rapidement les problèmes de performances des bases de données en fournissant des détails tels que les suivants :
* N premières requêtes de longue durée et tendance associée.
* La possibilité d’accéder aux détails d’une requête, d’afficher le texte de la requête ainsi que l’historique de l’exécution indiquant le temps de requête minimal, maximal, moyen et d’écart type
* Utilisation des ressources (processeur, mémoire et stockage)
 
Dans ce tutoriel, vous allez apprendre à utiliser les journaux des requêtes lentes MySQL, l’outil Log Analytics ou le modèle Classeurs pour visualiser les analyses des performances des requêtes pour Azure Database pour MySQL - Serveur flexible. 

## <a name="prerequisites"></a>Prérequis
- Vous devez créer une instance d’Azure Database pour MySQL - Serveur Flexible. Pour obtenir une procédure pas à pas, reportez-vous à [Créer une instance d’Azure Database pour MySQL - Serveur flexible](./quickstart-create-server-portal.md).
- Vous devez créer un espace de travail Log Analytics. Pour obtenir une procédure pas à pas, reportez-vous à [Créer un espace de travail Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
>[!div class="checklist"]
> * Configurer les journaux des requêtes lentes à partir du portail ou avec Azure CLI
> * Configurer les diagnostics
> * Afficher les requêtes lentes avec Log Analytics 
> * Afficher les requêtes lentes avec des classeurs 

## <a name="configure-slow-query-logs-from-portal"></a>Configurer les journaux des requêtes lentes à partir du portail 


1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez votre serveur flexible.

1. Dans la section **Paramètres** dans l’encadré, sélectionnez **Paramètres du serveur**.
   :::image type="content" source="./media//tutorial-query-performance-insights/server-parameters.png" alt-text="Page Paramètres du serveur.":::

1. Mettez à jour le paramètre **slow_query_log** en le définissant sur **ACTIVÉ**.
   :::image type="content" source="./media/tutorial-query-performance-insights/slow-query-log-enable.png" alt-text="Activation des journaux des requêtes lentes.":::

1. Changez tous les autres paramètres nécessaires (par exemple, `long_query_time`, `log_slow_admin_statements`). Pour plus de paramètres, consultez la documentation sur les [journaux des requêtes lentes](./concepts-slow-query-logs.md#configure-slow-query-logging).  
   :::image type="content" source="./media/tutorial-query-performance-insights/long-query-time.png" alt-text="Mise à jour des paramètres relatifs au journal des requêtes lentes.":::

1. Sélectionnez **Enregistrer**. 
   :::image type="content" source="./media/tutorial-query-performance-insights/save-parameters.png" alt-text="Enregistrement des paramètres du journal des requêtes lentes.":::

À partir de la page **Paramètres du serveur**, vous pouvez revenir à la liste des journaux en fermant la page.



## <a name="configure-slow-query-logs-from-azure-cli"></a>Configurer les journaux des requêtes lentes à partir d’Azure CLI
 
Si vous souhaitez effectuer les tâches ci-dessus avec Azure CLI, vous pouvez activer et configurer les journaux des requêtes lentes pour votre serveur en utilisant l’interface CLI. 

> [!IMPORTANT]
> Il est recommandé de ne consigner que les types d’événements et les utilisateurs requis à des fins d’audit pour garantir que les performances de votre serveur ne sont pas fortement affectées.

Activez et configurez les journaux de requêtes lentes pour votre serveur.

```azurecli
# Turn on statement level log

az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec

# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries

az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs



az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="set-up-diagnostics"></a>Configurer les diagnostics

Les journaux de requêtes lentes sont intégrés avec les paramètres de diagnostic Azure Monitor pour vous permettre de canaliser vos journaux vers les journaux Azure Monitor, Event Hubs ou Stockage Azure.

1. En dessous de la section **Supervision** dans la barre latérale, sélectionnez **Paramètres de diagnostic** > **Ajouter des paramètres de diagnostic**.

   :::image type="content" source="./media/tutorial-query-performance-insights/add-diagnostic-setting.png" alt-text="Capture d’écran des options Paramètres de diagnostic":::

1. Entrez un nom de paramètre de diagnostic.

1. Spécifiez les destinations auxquelles envoyer les journaux des requêtes lentes (espace de travail Log Analytics, compte de stockage ou hub d’événements).

    >[!Note]
    > Dans le cadre du tutoriel, nous devons envoyer les journaux des requêtes lentes à l’espace de travail Log Analytics.

1. Sélectionnez **MySqlSlowLogs** comme type de journal.
    :::image type="content" source="./media/tutorial-query-performance-insights/configure-diagnostic-setting.png" alt-text="Capture d’écran des options de configuration des paramètres de diagnostic":::

1. Après avoir configuré les récepteurs de données auxquels envoyer les journaux des requêtes lentes, sélectionnez **Enregistrer**.
    :::image type="content" source="./media/tutorial-query-performance-insights/save-diagnostic-setting.png" alt-text="Capture d’écran des options de configuration des paramètres de diagnostic, avec l’option Enregistrer mise en évidence":::

    >[!Note]
    >Vous devez créer des récepteurs de données (espace de travail Log Analytics, compte de stockage ou hub d’événements) avant de configurer les paramètres de diagnostic. Vous pouvez accéder aux journaux des requêtes lentes dans les récepteurs de données que vous avez configurés (espace de travail Log Analytics, compte de stockage ou hub d’événements). L’affichage des journaux peut prendre jusqu’à 10 minutes.
 
## <a name="view-query-insights-using-log-analytics"></a>Afficher les insights de requête en utilisant Log Analytics 

Dans la section **Supervision**, accédez à **Journaux**. Fermez la fenêtre **Requêtes**.

:::image type="content" source="./media/tutorial-query-performance-insights/log-query.png" alt-text="Capture d’écran de Log Analytics":::

Dans la fenêtre de requête, vous pouvez écrire la requête à exécuter.  Ici, nous avons utilisé une requête pour rechercher les requêtes de plus de 10 secondes sur un serveur particulier.

```kusto
 AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
```
    
Sélectionnez l’**intervalle de temps** et **exécutez** la requête. Voici les résultats de la requête.  

:::image type="content" source="./media/tutorial-query-performance-insights/slow-query.png" alt-text="Capture d’écran du journal des requêtes lentes":::

## <a name="view-query-insights-using-workbooks"></a>Afficher les insights de requête avec des classeurs 

1.  Dans le portail Azure, accédez au panneau **Supervision** pour Azure Database pour MySQL - Serveur flexible et sélectionnez **Classeurs**.
2.  Vous devriez être en mesure de voir les modèles. Sélectionnez **Query Performance Insight**. 

    :::image type="content" source="./media/tutorial-query-performance-insights/monitor-workbooks.png" alt-text="Capture d’écran du modèle de classeur":::

Vous pouvez voir la visualisation suivante : 
>[!div class="checklist"]
> * Chargement de requête
> * Nombre total de connexions actives
> * Tendance des requêtes lentes (durée des requêtes supérieure à 10 secondes)
> * Détails des requêtes lentes
> * Lister les 5 requêtes les plus longues
> * Résumer les requêtes lentes selon le temps de requête minimal, maximal, moyen et d’écart type

    
:::image type="content" source="./media/tutorial-query-performance-insights/long-query.png" alt-text="Capture d’écran des requêtes longues":::

>[!Note]
> * Pour l’utilisation des ressources, vous pouvez utiliser le modèle Vue d’ensemble.
> * Vous pouvez également modifier ces modèles afin de les personnaliser en fonction de vos besoins. Pour plus d’informations, consultez [Vue d’ensemble des classeurs Azure Monitor - Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).
> * Pour une vue rapide, vous pouvez également exécuter un test ping sur les classeurs ou la requête Log Analytics dans votre tableau de bord. Pour plus d’informations, consultez [Créer un tableau de bord dans le portail Azure - Portail Azure](../../azure-portal/azure-portal-dashboards.md). 

Dans Query Performance Insight, deux métriques peuvent vous aider à trouver les goulots d’étranglement potentiels : le nombre d’exécutions et la durée. Les requêtes de longue durée ont le plus grand risque de verrouiller des ressources le plus longtemps, de bloquer d’autres utilisateurs et de limiter l’évolutivité. Dans certains cas, un nombre d’exécutions élevé peut augmenter le nombre d’allers-retours réseau. Les allers-retours réseau affectent les performances. Ils peuvent entraîner une latence du réseau et une latence du serveur en aval. Le nombre d’exécutions peut donc faciliter la recherche des requêtes fréquemment exécutées (« bavardes »). Ces requêtes peuvent être facilement optimisées. 

## <a name="next-steps"></a>Étapes suivantes
- [Démarrez avec les classeurs Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#visualizations) et découvrez-en plus sur les nombreuses options pour les visualisations enrichies des classeurs.
- En savoir plus sur les [journaux des requêtes lentes](concepts-slow-query-logs.md)


