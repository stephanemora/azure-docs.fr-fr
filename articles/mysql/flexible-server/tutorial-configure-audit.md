---
title: 'Tutoriel : Audit pour Azure Database pour MySQL - Serveur flexible'
description: 'Tutoriel : Audit pour Azure Database pour MySQL - Serveur flexible'
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: b4c8206071ce71cc29a9de45b46fa05c40f9cd91
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621256"
---
# <a name="tutorial-auditing-for-azure-database-for-mysql--flexible-server"></a>Tutoriel : Audit pour Azure Database pour MySQL - Serveur flexible
[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Le serveur flexible Azure Database pour MySQL offre aux utilisateurs la possibilité de configurer des journaux d’audit. Les journaux d’audit peuvent être utilisés pour suivre l’activité au niveau de la base de données, y compris les événements relatifs aux connexions, à l’administration, au langage de description de données et au langage de manipulation de données. Ces types de journaux sont couramment utilisés à des fins de conformité. L’audit de base de données est généralement utilisé pour :
* Prendre en compte toutes les actions qui se produisent au sein d’un schéma, d’une table ou d’une ligne spécifique ou qui affectent un contenu spécifique
* Empêcher les utilisateurs (ou d’autres personnes) d’effectuer des actions inappropriées en fonction de leur responsabilité
* Investiguer une activité suspecte
* Superviser et collecter des données sur des activités de base de données spécifiques
 
Dans ce tutoriel, vous allez apprendre à utiliser les journaux d’audit MySQL, l’outil Log Analytics ou le modèle Classeurs afin de visualiser les informations d’audit pour Azure Database pour MySQL - Serveur flexible. 

## <a name="prerequisites"></a>Prérequis
- Vous devez créer une instance d’Azure Database pour MySQL - Serveur Flexible. Pour obtenir une procédure pas à pas, reportez-vous à [Créer une instance d’Azure Database pour MySQL - Serveur flexible](./quickstart-create-server-portal.md).
- Vous devez créer un espace de travail Log Analytics. Pour obtenir une procédure pas à pas, reportez-vous à [Créer un espace de travail Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
>[!div class="checklist"]
> * Configurer l’audit à partir du portail ou avec Azure CLI
> * Configurer les diagnostics
> * Afficher les journaux d’audit en utilisant Log Analytics 
> * Afficher les journaux d’audit en utilisant des classeurs 

## <a name="configure-auditing-from-portal"></a>Configurer l’audit à partir du portail 


1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez votre serveur flexible.

1. Dans la section **Paramètres** dans l’encadré, sélectionnez **Paramètres du serveur**.
    :::image type="content" source="./media/tutorial-configure-audit/server-parameters.png" alt-text="Paramètres du serveur":::

1. Mettez à jour le paramètre **audit_log_enabled** sur ON.
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-enabled.png" alt-text="Activer les journaux d’audit":::

1. Sélectionnez les [types d’événements](concepts-audit-logs.md#configure-audit-logging) à enregistrer en mettant à jour le paramètre **audit_log_events**.
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-events.png" alt-text="Événements du journal d’audit":::

1. Ajoutez des utilisateurs MySQL à inclure dans la journalisation ou à exclure de celle-ci en mettant à jour les paramètres **audit_log_exclude_users** et **audit_log_include_users**. Spécifiez les utilisateurs en fournissant leur nom d’utilisateur MySQL.
    :::image type="content" source="./media/tutorial-configure-audit/audit-log-exclude-users.png" alt-text="Utilisateurs à exclure du journal d’audit":::

1. Une fois que vous avez modifié les paramètres, vous pouvez cliquer sur **Enregistrer**. Vous pouvez également **Abandonner** vos modifications.
    :::image type="content" source="./media/tutorial-configure-audit/save-parameters.png" alt-text="Save":::



## <a name="configure-auditing--from-azure-cli"></a>Configurer l’audit à partir d’Azure CLI
 
Si vous souhaitez effectuer les tâches ci-dessus avec Azure CLI, vous pouvez activer et configurer l’audit pour votre serveur en utilisant l’interface CLI. 

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```


## <a name="set-up-diagnostics"></a>Configurer les diagnostics

Les journaux d’audit sont intégrés aux paramètres de diagnostic Azure Monitor pour vous permettre de canaliser vos journaux vers les journaux Azure Monitor, Event Hubs ou Stockage Azure.

1. Dans la section **Supervision** dans la barre latérale, sélectionnez **Paramètres de diagnostic**.

1. Cliquez sur « + Ajouter un paramètre de diagnostic » :::image type="content" source="./media/tutorial-configure-audit/add-diagnostic-setting.png" alt-text="Ajouter un paramètre de diagnostic":::

1. Entrez un nom de paramètre de diagnostic.

1. Spécifiez les destinations auxquelles envoyer les journaux d’audit (espace de travail Log Analytics, compte de stockage et/ou hub d’événements).
    >[!Note]
    > Dans le cadre du tutoriel, nous devons envoyer les journaux des requêtes lentes à l’espace de travail Log Analytics.
    
1. Sélectionnez **MySqlAuditLogs** comme type de journal.
    :::image type="content" source="./media/tutorial-configure-audit/configure-diagnostic-setting.png" alt-text="Configurer un paramètre de diagnostic":::

1. Une fois que vous avez configuré les récepteurs de données auxquels envoyer les journaux d’audit, vous pouvez cliquer sur **Enregistrer**.
    :::image type="content" source="./media/tutorial-configure-audit/save-diagnostic-setting.png" alt-text="Enregistrer un paramètre de diagnostic":::

    >[!Note]
    >Vous devez créer des récepteurs de données (espace de travail Log Analytics, compte de stockage ou hub d’événements) avant de configurer les paramètres de diagnostic. Vous pouvez accéder aux journaux des requêtes lentes dans les récepteurs de données que vous avez configurés (espace de travail Log Analytics, compte de stockage ou hub d’événements). L’affichage des journaux peut prendre jusqu’à 10 minutes.

## <a name="view-audit-logs-using-log-analytics"></a>Afficher les journaux d’audit en utilisant Log Analytics 

Dans la section **Supervision**, accédez à **Journaux**. Fermez la fenêtre **Requêtes**.  

:::image type="content" source="./media/tutorial-configure-audit/log-query.png" alt-text="Capture d’écran de Log Analytics":::

Dans la fenêtre de requête, vous pouvez écrire la requête à exécuter.  Ici, nous avons utilisé la requête pour rechercher le résumé des événements audités sur un serveur particulier.

```kusto
AzureDiagnostics
    |where Category =='MySqlAuditLogs' 
    |project TimeGenerated, Resource, event_class_s, event_subclass_s, event_time_t, user_s ,ip_s , sql_text_s 
    |summarize count() by event_class_s,event_subclass_s 
    |order by event_class_s 
```
  
:::image type="content" source="./media/tutorial-configure-audit/audit-query.png" alt-text="Capture d’écran de la requête Log Analytics":::

## <a name="view-audit-logs-using-workbooks"></a>Afficher les journaux d’audit en utilisant des classeurs 
 
Le modèle de classeur que nous utilisons pour l’audit nous oblige à créer des paramètres de diagnostic pour envoyer des journaux de plateforme. 

1.  Pour envoyer des journaux de plateforme, dans le menu Azure Monitor, cliquez sur Journal d’activité, puis sur **Paramètres de diagnostic**. 

    :::image type="content" source="./media/tutorial-configure-audit/activity-diagnostics.png" alt-text="capture d’écran des paramètres de diagnostic":::

2.  Ajoutez un nouveau paramètre ou modifiez un paramètre existant. Chaque paramètre ne peut pas avoir plus d’un type de destination.

    :::image type="content" source="./media/tutorial-configure-audit/activity-settings-diagnostic.png" alt-text="Capture d’écran de la sélection des paramètres de diagnostic":::

    >[!Note]
    >Vous pouvez accéder aux journaux des requêtes lentes dans les récepteurs de données que vous avez configurés (espace de travail Log Analytics, compte de stockage, hub d’événements). L’affichage des journaux peut prendre jusqu’à 10 minutes.

3.  Dans le portail Azure, accédez au panneau **Supervision** pour Azure Database pour MySQL - Serveur flexible et sélectionnez **Classeurs**.
4.  Vous devriez être en mesure de voir les modèles. Sélectionnez **Audit**. 

    :::image type="content" source="./media/tutorial-configure-audit/monitor-workbooks.png" alt-text="Capture d’écran du modèle de classeur":::
    
Vous pouvez voir la visualisation suivante : 
>[!div class="checklist"]
> * Actions administratives sur le service
> * Résumé de l’audit
> * Résumé de l’audit des événements de connexion
> * Auditer les événements de connexion
> * Résumé de l’accès aux tables
> * Erreurs identifiées


:::image type="content" source="./media/tutorial-configure-audit/admin-events.png" alt-text="Capture d’écran des événements d’administration avec le modèle de classeur":::

:::image type="content" source="./media/tutorial-configure-audit/audit-summary.png" alt-text="Capture d’écran des événements de résumé d’audit avec le modèle de classeur":::

>[!Note]
> * Vous pouvez également modifier ces modèles afin de les personnaliser en fonction de vos besoins. Pour plus d’informations, consultez [Vue d’ensemble des classeurs Azure Monitor - Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).
> * Pour une vue rapide, vous pouvez également exécuter un test ping sur les classeurs ou la requête Log Analytics dans votre tableau de bord. Pour plus d’informations, consultez [Créer un tableau de bord dans le portail Azure - Portail Azure](../../azure-portal/azure-portal-dashboards.md). 

Les actions d’administration sur le service fournissent des détails sur l’activité effectuée sur ce dernier. Cette visualisation vous aide à déterminer la nature et l’auteur de toute opération d’écriture (PUT, POST, DELETE) effectuée sur les ressources dans votre abonnement ainsi que le moment auquel cette opération a eu lieu. 

Les autres visualisations vous aident à obtenir des détails sur l’activité de la base de données. La sécurité de la base de données est composée de quatre parties. Il s’agit de la sécurité du serveur, de la connexion de base de données, du contrôle d’accès aux tables et de la restriction d’accès à la base de données. La sécurité du serveur a pour responsabilité d’empêcher les personnes non autorisées d’accéder à la base de données. Concernant les connexions de base de données, l’administrateur doit également vérifier si les mises à jour effectuées sur la base de données sont réalisées par le personnel autorisé. La restriction de l’accès à la base de données est particulièrement importante pour ceux dont la base de données est chargée sur Internet. Cela aide à empêcher toute source externe d’entrer dans votre base de données ou d’y accéder. 

## <a name="next-steps"></a>Étapes suivantes
- [Démarrez avec les classeurs Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#visualizations) et découvrez-en plus sur les nombreuses options pour les visualisations enrichies des classeurs.
- En savoir plus sur les [journaux d'audit](concepts-audit-logs.md)