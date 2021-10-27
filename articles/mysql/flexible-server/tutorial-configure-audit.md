---
title: 'Tutoriel : Configurer des journaux d’audit avec Serveur flexible Azure Database pour MySQL'
description: Ce tutoriel vous montre comment configurer des journaux d’audit en utilisant Serveur flexible Azure Database pour MySQL.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: tutorial
ms.date: 10/01/2021
ms.openlocfilehash: 4a819814b8c5bb8cf6d4bea949463fddd05405a8
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065860"
---
# <a name="tutorial-configure-audit-logs-by-using-azure-database-for-mysql-flexible-server"></a>Tutoriel : Configurer des journaux d’audit avec Serveur flexible Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Vous pouvez utiliser Serveur flexible Azure Database pour MySQL pour configurer des journaux d’audit. Les journaux d’audit peuvent servir à suivre l’activité au niveau de la base de données, notamment les événements de connexion, d’administration, de langage de définition de données (DDL) et de langage de manipulation de données (DML). Ces types de journaux sont couramment utilisés à des fins de conformité. En général, vous utilisez un audit de base de données dans les buts suivants :
* Tenir compte de toutes les actions qui se produisent dans un schéma, une table ou une ligne spécifique, ou qui affectent un contenu spécifique.
* Empêcher les utilisateurs (ou d’autres personnes) d’effectuer des actions inappropriées en fonction de leur responsabilité.
* Investiguer une activité suspecte.
* Superviser et collecter des données sur des activités de base de données spécifiques.
 
Cet article explique comment utiliser les journaux d’audit MySQL, les outils Log Analytics ou un modèle de classeur pour visualiser des informations d’audit pour Serveur flexible Azure Database pour MySQL.

Ce didacticiel vous montre comment effectuer les opérations suivantes :
>[!div class="checklist"]
> * Configurer l’audit en utilisant le portail Azure ou Azure CLI
> * Configurer les diagnostics
> * Afficher les journaux d’audit en utilisant Log Analytics 
> * Afficher les journaux d’audit en utilisant des classeurs  

## <a name="prerequisites"></a>Prérequis

- [Créez une instance de Serveur flexible Azure Database pour MySQL](./quickstart-create-server-portal.md).
- [Créez un espace de travail Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

## <a name="configure-auditing-by-using-the-azure-portal"></a>Configurer un audit à l’aide du portail Azure 

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sélectionnez votre instance de serveur flexible.

1. Dans le volet gauche, sous **Paramètres**, sélectionnez **Paramètres du serveur**.

    :::image type="content" source="./media/tutorial-configure-audit/server-parameters.png" alt-text="Capture d’écran montrant la liste « Paramètres du serveur ».":::

1. Pour le paramètre **audit_log_enabled**, sélectionnez **ON**.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-enabled.png" alt-text="Capture d’écran montrant le paramètre « audit_log_enabled » en position « ON ».":::

1. Pour le paramètre **audit_log_events**, dans la liste déroulante, sélectionnez les [types d’événements](concepts-audit-logs.md#configure-audit-logging) à journaliser.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-events.png" alt-text="Capture d’écran des options d’événement dans la liste déroulante « audit_log_events ».":::

1. Pour les paramètres **audit_log_exclude_users** et **audit_log_include_users**, spécifiez les utilisateurs MySQL à inclure dans la journalisation ou à exclure de celle-ci en fournissant leurs noms d’utilisateur MySQL.

    :::image type="content" source="./media/tutorial-configure-audit/audit-log-exclude-users.png" alt-text="Capture d’écran montrant les noms d’utilisateur MySQL à inclure dans la journalisation ou à exclure.":::

1. Sélectionnez **Enregistrer**.

    :::image type="content" source="./media/tutorial-configure-audit/save-parameters.png" alt-text="Capture d’écran du bouton « Enregistrer » permettant d’enregistrer les modifications apportées aux valeurs des paramètres.":::


## <a name="configure-auditing-by-using-the-azure-cli"></a>Configurer un audit à l’aide d’Azure CLI
 
Vous pouvez aussi activer et configurer un audit pour votre serveur flexible à partir d’Azure CLI en exécutant la commande suivante : 

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```


## <a name="set-up-diagnostics"></a>Configurer les diagnostics

Les journaux d’audit sont intégrés aux paramètres de diagnostic Azure Monitor pour vous permettre de canaliser vos journaux vers l’un des trois récepteurs de données suivants :
* Un espace de travail Log Analytics
* Un hub d’événements
* Un compte de stockage

>[!Note]
>Vous devez créer vos récepteurs de données avant de configurer les paramètres de diagnostic. Vous pouvez accéder aux journaux d’audit dans les récepteurs de données que vous avez configurés. L’affichage des journaux peut prendre jusqu’à 10 minutes.

1. Dans le volet de gauche, sous **Supervision**, sélectionnez **Paramètres de diagnostic**.

1. Dans le volet **Paramètres de diagnostic**, sélectionnez **Ajouter un paramètre de diagnostic**.

    :::image type="content" source="./media/tutorial-configure-audit/add-diagnostic-setting.png" alt-text="Capture d’écran du lien « Ajouter un paramètre de diagnostic » dans le volet « Paramètres de diagnostic ».":::

1. Dans la zone **Nom**, entrez un nom pour le paramètre de diagnostic.

    :::image type="content" source="./media/tutorial-configure-audit/configure-diagnostic-setting.png" alt-text="Capture d’écran du volet « Paramètres de diagnostic » permettant de sélectionner des options de configuration.":::

1. Spécifiez les destinations (espace de travail Log Analytics, hub d’événements ou compte de stockage) auxquelles envoyer les journaux d’audit en cochant leurs cases correspondantes.

    >[!Note]
    > Pour ce tutoriel, vous allez envoyer les journaux d’audit à un espace de travail Log Analytics.
    
1. Sous **Journal**, pour le type de journal, cochez la case **MySqlAuditLogs**.

1. Après avoir configuré les récepteurs de données vers lesquels canaliser les journaux d’audit, sélectionnez **Enregistrer**.

    :::image type="content" source="./media/tutorial-configure-audit/save-diagnostic-setting.png" alt-text="Capture d’écran du bouton « Enregistrer » situé en haut du volet « Paramètres de diagnostic ».":::

## <a name="view-audit-logs-by-using-log-analytics"></a>Afficher les journaux d’audit en utilisant Log Analytics 

1. Dans Log Analytics, dans le volet gauche, sous **Supervision**, sélectionnez **Journaux**.

1. Fermez la fenêtre **Requêtes**.  

   :::image type="content" source="./media/tutorial-configure-audit/log-query.png" alt-text="Capture d’écran du volet « Requêtes » de Log Analytics.":::

1. Dans la fenêtre de requête, vous pouvez écrire la requête à exécuter. Par exemple, pour trouver un résumé des événements audités sur un serveur particulier, nous avons utilisé la requête suivante : 

    ```kusto
    AzureDiagnostics
        |where Category =='MySqlAuditLogs' 
        |project TimeGenerated, Resource, event_class_s, event_subclass_s, event_time_t, user_s ,ip_s , sql_text_s 
        |summarize count() by event_class_s,event_subclass_s 
        |order by event_class_s 
    ```
    
    :::image type="content" source="./media/tutorial-configure-audit/audit-query.png" alt-text="Capture d’écran d’un exemple de requête Log Analytics qui cherche à trouver un résumé des événements audités sur un serveur particulier.":::

## <a name="view-audit-logs-by-using-workbooks"></a>Afficher les journaux d’audit en utilisant des classeurs 
 
Le modèle de classeur que vous utilisez pour l’audit vous oblige à créer des paramètres de diagnostic pour envoyer des journaux de plateforme. 

1. Dans Azure Monitor, dans le volet gauche, sélectionnez **Journal d’activité**, puis sélectionnez **Paramètres de diagnostic**. 

    :::image type="content" source="./media/tutorial-configure-audit/activity-diagnostics.png" alt-text="Capture d’écran montrant l’onglet « Paramètres de diagnostic » dans le volet Azure Monitor « Journal d’activité ».":::

1. Dans le volet **Paramètre de diagnostic**, vous pouvez ajouter un nouveau paramètre ou en modifier un existant. Chaque paramètre peut avoir un seul type de destination.

    :::image type="content" source="./media/tutorial-configure-audit/activity-settings-diagnostic.png" alt-text="Capture d’écran du volet Azure Monitor « Paramètre de diagnostic » permettant de sélectionner des destinations de journal.":::

    > [!Note]
    > Vous pouvez accéder aux journaux des requêtes lentes dans les récepteurs de données (espace de travail Log Analytics, compte de stockage ou hub d’événements) que vous avez déjà configurés. L’affichage des journaux peut prendre jusqu’à 10 minutes.

1. Dans le portail Azure, dans le volet gauche, sous **Supervision** pour votre instance de Serveur flexible Azure Database pour MySQL, sélectionnez **Classeurs**.
1. Sélectionnez le classeur **Audit**. 

    :::image type="content" source="./media/tutorial-configure-audit/monitor-workbooks.png" alt-text="Capture d’écran montrant tous les classeurs dans la galerie de classeurs.":::
    
Dans le classeur, vous pouvez voir les visualisations suivantes : 
>[!div class="checklist"]
> * Actions administratives sur le service
> * Résumé de l’audit
> * Résumé de l’audit des événements de connexion
> * Audit des événements de connexion
> * Résumé de l’accès aux tables
> * Erreurs identifiées


:::image type="content" source="./media/tutorial-configure-audit/admin-events.png" alt-text="Capture d’écran du modèle de classeur « Actions administratives sur le service ».":::

:::image type="content" source="./media/tutorial-configure-audit/audit-summary.png" alt-text="Capture d’écran du modèle de classeur « Auditer les événements de connexion ».":::

>[!Note]
> * Vous pouvez aussi modifier ces modèles et les personnaliser selon vos besoins. Pour plus d’informations, consultez la section « Mode d’édition » de la [vue d’ensemble des classeurs Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode).
> * Pour une consultation rapide, vous pouvez aussi épingler les classeurs ou la requête Log Analytics sur votre tableau de bord. Pour plus d’informations, consultez [Créer un tableau de bord dans le portail Azure](../../azure-portal/azure-portal-dashboards.md). 

La vue *Actions d’administration sur le service* vous fournit des détails sur l’activité effectuée sur ce dernier. Elle vous aide à déterminer *la nature, l’auteur et la date* de toute opération d’écriture (PUT, POST, DELETE) effectuée sur les ressources dans votre abonnement. 

Vous pouvez utiliser d’autres visualisations pour mieux comprendre les détails de l’activité de la base de données. La sécurité d’une base de données comporte quatre aspects : 
* **Sécurité du serveur** : Responsable d’empêcher les personnes non autorisées d’accéder à la base de données.  
* **Connexion de base de données** : L’administrateur doit vérifier si des mises à jour de base de données ont été effectuées par le personnel autorisé.
* **Contrôle d’accès aux tables** : Montre les clés d’accès des utilisateurs autorisés et les tables au sein de la base de données que chacun d’eux est autorisé à gérer.
* **Restriction d’accès à la base de données** : Particulièrement importante pour les personnes qui ont chargé une base de données sur Internet car elle permet d’empêcher des sources externes d’accéder à votre base de données. 

## <a name="next-steps"></a>Étapes suivantes
- [Découvrez-en plus sur les classeurs Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#visualizations) et leurs options de visualisation enrichies.
- [Découvrez-en plus sur les journaux d’audit](concepts-audit-logs.md).
