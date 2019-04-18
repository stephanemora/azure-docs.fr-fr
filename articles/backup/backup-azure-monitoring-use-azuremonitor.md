---
title: 'Sauvegarde Azure : Surveiller la sauvegarde Azure avec Azure Monitor'
description: Surveiller les charges de travail de sauvegarde Azure et créer des alertes personnalisées à l’aide d’Azure Monitor
services: backup
author: pvrk
manager: shivamg
keywords: Analytique de journal ; Sauvegarde Azure ; Alertes ; Paramètres de diagnostic ; Groupes d’actions
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 15bb64917fa58ba2d13c6f372640957508ab29c1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494557"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Surveillance à l’échelle à l’aide d’Azure Monitor

Le [intégrées de surveillance et génération d’alertes de l’article](backup-azure-monitoring-built-in-monitor.md) répertorié la surveillance et les fonctionnalités dans un coffre Recovery services unique et qui est disponible sans aucune infrastructure de gestion supplémentaire d’alerte. Toutefois, le service intégré est limité dans les scénarios suivants.

- Données de surveillance de plusieurs coffres RS entre des abonnements
- Si l’e-mail n’est pas le canal de notification par défaut
- Si les utilisateurs souhaitent recevoir des alertes pour d’autres scénarios
- Si vous souhaitez afficher les informations dans le composant local tel que System Center DPM (SC-DPM) dans Azure, qui n’est pas affiché dans [travaux de sauvegarde](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) ou [alertes de sauvegarde](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) dans le portail.

## <a name="using-log-analytics-workspace"></a>À l’aide d’espace de travail Log Analytique

> [!NOTE]
> Données à partir de sauvegardes de machine virtuelle Azure, l’Agent MAB, System Center DPM (SC-DPM), les sauvegardes SQL dans des machines virtuelles Azure sont en cours pompées à l’espace de travail Analytique de journal via les paramètres de diagnostic. Prise en charge pour les sauvegardes de partage de fichiers Azure, serveur de sauvegarde Microsoft Azure (MABS) sera bientôt disponible.

Nous exploitons les fonctionnalités de deux services Azure - **les paramètres de Diagnostic** (pour envoyer des données à partir de plusieurs ressources Azure Resource Manager à une autre ressource) et **Analytique de journal** (LA - générer alertes personnalisées dans laquelle vous pouvez définir des autres canaux de notification à l’aide de groupes d’actions) pour l’analyse à grande échelle. Les sections suivantes détaillent comment utiliser LA pour surveiller la sauvegarde Azure à grande échelle.

### <a name="configuring-diagnostic-settings"></a>Configuration des paramètres de Diagnostic

Une ressource Azure Resource Manager, telles que le coffre Azure Recovery services enregistre toutes les informations possibles sur les opérations planifiées et les opérations de l’utilisateur a déclenché en tant que données de diagnostic. Cliquez sur « paramètres de Diagnostic » dans la section Surveillance et spécifier la cible pour les données de diagnostic du coffre RS.

![Paramètre de diagnostic coffre Recovery Services avec LA cible](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Vous pouvez sélectionner un espace de travail de LA à partir d’un autre abonnement comme cible. *En sélectionnant l’espace de travail LA même pour plusieurs coffres RS, vous pouvez surveiller les coffres entre des abonnements dans un emplacement unique.* Sélectionnez « AzureBackupReport » en tant que le journal pour tous les canaux Azure sauvegarde les informations relatives à l’espace de travail de LA.

> [!IMPORTANT]
> Une fois que vous avez terminé la configuration, vous devez attendre 24 heures pour l’installation push de données initiales terminer. Par la suite, tous les événements sont envoyés comme indiqué dans le [section fréquence](#diagnostic-data-update-frequency).

### <a name="deploying-solution-to-log-analytics-workspace"></a>Déploiement de solution dans l’espace de travail Analytique de journal

Une fois les données au sein de l’espace de travail de LA, [déployer un modèle github](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) sur LA pour visualiser les données. Vérifiez que vous donnez le même groupe de ressources, nom de l’espace de travail et emplacement de l’espace de travail pour identifier correctement l’espace de travail, puis installer ce modèle sur celui-ci.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Afficher les données de sauvegarde Azure à l’aide d’Analytique de journal (LA)

Une fois que le modèle est déployé, la solution de surveillance de sauvegarde Azure s’afficheront dans la région de résumé d’espace de travail. Vous pouvez parcourir par le biais de

- Azure Monitor -> « Plus » sous la section « Insights » et choisissez l’espace de travail approprié ou
- Espaces de travail log Analytique -> sélectionnez l’espace de travail -> « Espace de travail Résumé » sous la section Général.

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Lorsque vous cliquez sur la vignette, le modèle de concepteur s’ouvre une série de graphiques sur les données de surveillance de base à partir de la sauvegarde Azure comme

#### <a name="all-backup-jobs"></a>Tous les travaux de sauvegarde

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>Travaux de restauration

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Alertes de sauvegarde Azure intégrés pour les ressources Azure

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>Alertes de sauvegarde Azure intégrés pour les ressources locales

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>Sources de données Active

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>Stockage de Cloud de coffre Recovery Services

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Les graphiques ci-dessus sont fournis avec le modèle et le client est libre de modifier/ajouter des graphiques plus.

> [!IMPORTANT]
> Lorsque vous déployez le modèle, il crée en fait un verrou en lecture seule et vous devez la supprimer pour modifier le modèle et l’enregistrer. Pour supprimer les verrous, recherchez dans le volet « Verrous » sous la section « Paramètres » de l’espace de travail Analytique de journal.

### <a name="create-alerts-using-log-analytics"></a>Créer des alertes à l’aide d’Analytique de journal

Azure Monitor permet aux utilisateurs de créer leurs propres alertes à partir de l’espace de travail de LA dans laquelle vous pouvez *exploiter les groupes d’actions de Azure pour sélectionner votre mécanisme de notification par défaut*. Cliquez sur l’un des graphiques ci-dessus pour ouvrir la section « Journaux » de l’espace de travail de LA ***où vous pouvez modifier les requêtes et créer des alertes sur les***.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Cliquez sur « Nouvelle règle d’alerte » comme indiqué ci-dessus pour ouvrir l’écran de création d’alerte Azure Monitor.

Comme vous pouvez le constater ci-dessous, la ressource est déjà marquée comme l’espace de travail de LA et intégration des groupes d’action est fournie.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> Veuillez noter que l’impact sur la tarification pertinentes de la création de cette requête est fourni [ici](https://azure.microsoft.com/pricing/details/monitor/).

#### <a name="alert-condition"></a>Condition d’alerte

L’aspect clé est la condition de déclenchement de l’alerte. En cliquant sur « Condition » chargera automatiquement la requête Kusto dans l’écran « Logs » comme indiqué ci-dessous, et vous pouvez le modifier pour l’adapter à votre scénario. Certains exemples de requêtes Kusto sont fournies dans le [section ci-dessous](#sample-kusto-queries).

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Modifiez la requête Kusto, si nécessaire, sélectionnez le seuil de droite (qui décidera lorsque l’alerte sera déclenchée), la période de droite (fenêtre de temps pour lequel la requête est exécutée) et la fréquence. Pour par exemple : Si le seuil est supérieur à 0, la période est de 5 minutes et la fréquence est de 5 minutes, puis la règle est traduite en tant que « Exécuter la requête toutes les 5 minutes pour les 5 dernières minutes et si le nombre de résultats est supérieur à 0, m’avertir par le groupe d’actions sélectionné »

#### <a name="action-group-integration"></a>Intégration des groupes d’action

Groupes d’actions spécifient les canaux de notification disponibles à l’utilisateur. En cliquant sur « Créer nouveau » sous « Groupes d’actions » section affiche la liste des mécanismes de notification disponibles.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

En savoir plus sur la [alertes à partir de l’espace de travail de LA](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) et environ [groupes d’actions](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) à partir de la documentation d’Azure Monitor.

Par conséquent, vous pouvez satisfaire toutes les alertes et surveillance des exigences à partir de LA seule ou utilisez-la comme une technique supplémentaire aux mécanismes de notification intégrés.

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

Les graphiques par défaut permet d’obtenir les requêtes Kusto pour les scénarios de base sur laquelle vous pouvez créer des alertes. Vous pouvez également les modifier pour obtenir les données que vous souhaitez recevoir des alertes sur. Nous proposons ici quelques exemples de requête Kusto que vous collez dans la fenêtre de « Logs » et ensuite créer une alerte sur la requête.

#### <a name="all-successful-backup-jobs"></a>Tous les travaux de sauvegarde réussis

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>Tous les échecs des travaux de sauvegarde

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>Tous les travaux de sauvegarde de machine virtuelle Azure réussites

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-sql-log-backup-jobs"></a>Tous les travaux de sauvegarde de journal SQL réussites

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-mab-agent-backup-jobs"></a>Tous les travaux de sauvegarde Microsoft Azure Backup Agent réussites

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

### <a name="diagnostic-data-update-frequency"></a>Fréquence de mise à jour de données de diagnostic

Les données de diagnostic à partir du coffre sont pompées LA l’espace de travail avec un décalage. Chaque événement arrive à l’espace de travail de LA ***avec un décalage de 20 à 30 minutes après vous être envoyée à partir du coffre RS.***

- Les alertes intégrées du service de sauvegarde (parmi toutes les solutions) sont envoyés dès qu’ils sont créés. Ce qui signifie qu’ils apparaissent généralement dans l’espace de travail LA après un délai de 20 à 30 minutes.
- Les travaux de restauration (parmi toutes les solutions) et les travaux de sauvegarde ad hoc sont envoyés dès qu’ils ***sont terminées***.
- Tâches de sauvegarde à partir de toutes les solutions (à l’exception de la sauvegarde SQL) sont envoyés dès qu’ils ***sont terminées***.
- Pour la sauvegarde SQL, dans la mesure où nous pouvons avoir des sauvegardes de journal toutes les 15 minutes, pour toutes les tâches planifiées terminées sauvegarde, y compris les journaux, les informations sont traités par lot et envoyées toutes les 6 heures.
- Toutes les autres informations telles que l’élément de sauvegarde, stratégie, les points de récupération, etc. sur toutes les solutions de stockage sont envoyées **au moins une fois par jour.**
- Une modification dans la configuration de sauvegarde telles que la modification de la stratégie, etc. de stratégie de modification déclenche une notification push de toutes les informations de sauvegarde.

## <a name="using-rs-vaults-activity-logs"></a>Journaux d’activité à l’aide du coffre Recovery Services

Vous pouvez également utiliser les journaux d’activité pour obtenir une notification pour les événements, tels que la réussite de la sauvegarde.

> [!CAUTION]
> **Notez que ceci est uniquement applicable aux sauvegardes de machines virtuelles Azure.** Vous ne pouvez pas utiliser cela pour d’autres solutions telles que l’Agent de sauvegarde Azure, les sauvegardes SQL dans Azure, etc. de fichiers Azure.

### <a name="sign-in-into-azure-portal"></a>Connectez-vous au portail Azure

Connectez-vous au portail Azure et continuer au coffre Azure Recovery Services approprié, puis cliquez sur la section « Journal d’activité » dans les propriétés.

### <a name="identify-appropriate-log-and-create-alert"></a>Identifier le journal approprié et de création d’alerte

Appliquez les filtres de l’image suivante pour vérifier si vous recevez bien les journaux d’activité en cas de sauvegardes réussies. Changez l’intervalle de temps pour afficher les enregistrements.

![Journaux d’activité pour les sauvegardes de machines virtuelles Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

Vous pouvez cliquer sur le segment « JSON » pour obtenir plus de détails et les afficher en les copiant-collant dans un éditeur de texte. Il doit afficher les détails du coffre et l’élément qui a déclenché l’activité de journaux, autrement dit, l’élément de sauvegarde.

Ensuite, cliquez sur « Ajouter une alerte de journal d’activité » afin de générer des alertes pour tous les journaux d’activité de ce type.

Vous pouvez cliquer sur « Ajouter alerte activité journal » ci-dessus et s’ouvre l’écran de création d’alerte qui est similaire à l’écran de création d’alerte [comme décrit ci-dessus](#create-alerts-using-log-analytics).

Ici la ressource est le coffre RS lui-même, et par conséquent, vous devez répéter la même action pour tous les coffres dans lequel vous souhaitez notification via les journaux d’activité. La condition sera inutile n’importe quel seuil, la période, la fréquence dans la mesure où il s’agit d’une alerte en fonction des événements. Dès que le journal d’activité pertinentes est généré, l’alerte est déclenchée.

## <a name="recommendation"></a>Recommandation

***Toutes les alertes créées à partir des journaux d’activité et espaces de travail de LA peuvent être affichées dans Azure Monitor dans le volet « Alertes » à gauche.***

Si la notification par le biais de journaux d’activité peut être utilisée, ***service sauvegarde Azure recommande vivement d’utiliser LA pour l’analyse à l’échelle et pas journaux d’activité pour les raisons suivantes***.

- **Scénarios limités :** Applicable uniquement pour les sauvegardes de machines virtuelles Azure et doivent être répétées pour chaque coffre Recovery Services.
- **Définition de fonction :** L’activité de sauvegarde planifiée ne correspond pas à la définition la plus récente des journaux d’activité et aligne [journaux de diagnostic](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-are-azure-monitor-diagnostic-logs). Ce prospect à impact inattendu lorsque les données de pompage via le canal de journal d’activité sont modifiées comme indiqué ci-dessous.
- **Problèmes avec le canal de journal d’activité :** Nous sommes passés à un nouveau modèle de pompage des journaux d’activité de Sauvegarde Azure sur les coffres Recovery Services. Malheureusement, le déplacement a affecté la génération des journaux d’activité dans des Clouds souverains Azure. Si les utilisateurs Azure souverain Cloud créé/configuré toutes les alertes à partir des journaux d’activité via Azure Monitor, ils ne seraient pas déclenchées. En outre, dans toutes les régions publiques Azure, si un utilisateur collecte des journaux d’activité Recovery Services dans un espace de travail Log Analytics, comme mentionné [ici](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), ces journaux d’activité n’apparaissent pas non plus.

Par conséquent, il est vivement recommandé d’utiliser espace de travail Log Analytics pour la surveillance et génération d’alertes à l’échelle de tous les sauvegarde Azure des charges de travail protégées.

## <a name="next-steps"></a>Étapes suivantes

- Reportez-vous à [modèle de données analytique de journal](backup-azure-log-analytics-data-model.md) pour créer des requêtes personnalisées.
