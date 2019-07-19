---
title: 'Sauvegarde Azure : surveiller la sauvegarde Azure avec Azure Monitor'
description: Surveiller les charges de travail de sauvegarde Azure et créer des alertes personnalisées à l’aide d’Azure Monitor
services: backup
author: pvrk
manager: shivamg
keywords: Analytique des journaux d'activité ; alertes ; paramètres de diagnostic ; groupes d’actions
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 7c53d8fe0ee5bbfdbe180aa4d18d8c7b7fab29c2
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295291"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Surveillance à l’échelle avec Azure Monitor

L’[article sur la surveillance intégrée et les alertes](backup-azure-monitoring-built-in-monitor.md) a répertorié les fonctionnalités de surveillance et d’alerte dans un coffre de services de récupération, proposé sans aucune infrastructure de gestion supplémentaire. Toutefois, le service intégré est limité aux scénarios suivants.

- Surveillance de données à partir de plusieurs coffres Recovery Services pour tous les abonnements
- Si l’e-mail n’est PAS le canal de notification favori
- Si les utilisateurs souhaitent également recevoir des alertes pour d’autres scénarios
- Si vous souhaitez afficher des informations provenant de composants locaux tels que System Center DPM (SC-DPM) dans Azure, qui ne sont pas visibles dans [Travaux de sauvegarde](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) ou dans [Alertes de sauvegarde](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) dans le portail.

## <a name="using-log-analytics-workspace"></a>Utilisation d’un espace de travail Log Analytics

> [!NOTE]
> Des données provenant de sauvegardes de machines virtuelles Azure, de l’agent MAB, System Center DPM (SC-DPM), de sauvegardes SQL et de sauvegardes de partage de fichiers Azure sont pompées dans l’espace de travail Log Analytics via des paramètres de diagnostic. La prise en charge du serveur de sauvegarde Azure (MABS) sera bientôt disponible.

Nous exploitons les fonctionnalités de deux services Azure : **Paramètres de diagnostic** (pour envoyer des données de plusieurs ressources Azure Resource Manager à une autre ressource) et **Log Analytics** (LA : pour générer des alertes personnalisées où vous pouvez définir d’autres canaux de notification à l’aide de groupes d’action) pour la surveillance à l’échelle. Les sections suivantes détaillent comment utiliser LA pour surveiller la sauvegarde Azure à l’échelle.

### <a name="configuring-diagnostic-settings"></a>Configuration de paramètres de diagnostic

Une ressource Azure Resource Manager telle que le coffre de services Azure Recovery enregistre toutes les informations possibles sur des opérations planifiées et des opérations déclenchées par l’utilisateur en tant que données de diagnostic. Cliquez sur « Paramètres de diagnostic » dans la section de surveillance et spécifiez la cible des données de diagnostic du coffre Recovery Services.

![Paramètre de diagnostic du coffre Recovery Services avec LA comme cible](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Vous pouvez sélectionner un espace de travail LA à partir d’un autre abonnement comme cible. *En sélectionnant le même espace de travail LA pour plusieurs coffres Recovery Services, vous pouvez surveiller les coffres de tous les abonnements à un emplacement unique.* Sélectionnez « AzureBackupReport » en tant que journal pour acheminer toutes les informations relatives à la sauvegarde Azure vers l’espace de travail LA.

> [!IMPORTANT]
> Après avoir terminé la configuration, vous devez attendre 24 heures jusqu’à ce que le push de données initial soit terminé. Ensuite, tous les événements sont envoyés comme indiqué dans la [section Fréquence](#diagnostic-data-update-frequency).

### <a name="deploying-solution-to-log-analytics-workspace"></a>Déploiement d’une solution dans l’espace de travail Log Analytics

Une fois que les données sont à l’intérieur de l’espace de travail LA, [déployez un modèle GitHub](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) sur LA pour les visualiser. Vérifiez que vous indiquez les mêmes groupe de ressources, nom de l’espace de travail et emplacement de l’espace de travail pour identifier correctement l’espace de travail, puis installez-y ce modèle.

> [!NOTE]
> Les utilisateurs qui n’ont pas d’alertes ou de tâches de sauvegarde/restauration dans leur espace de travail LA peuvent voir une erreur avec le code « BadArgumentError » sur le portail. Les utilisateurs peuvent ignorer cette erreur et continuer à utiliser la solution. Une fois que les données du type approprié commencent à circuler dans l’espace de travail, les visualisations sont semblables et les utilisateurs ne voient plus cette erreur.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Afficher des données de sauvegarde Azure avec Log Analytics (LA)

Une fois le modèle déployé, la solution de surveillance de la sauvegarde Azure s’affiche dans la région de résumé d’espace de travail. Vous pouvez la parcourir par le biais de

- Azure Monitor -> « Plus » sous la section « Insights » et choisir l’espace de travail approprié OU
- Espaces de travail Log Analytics -> sélectionner l’espace de travail -> « Résumé d’espace de travail » sous la section Général.

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Lorsque vous cliquez sur la vignette, le modèle de concepteur ouvre une série de graphiques sur les données de surveillance de base à partir de la sauvegarde Azure, tels que

#### <a name="all-backup-jobs"></a>Tous les travaux de sauvegarde

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>Travaux de restauration

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Alertes de sauvegarde Azure intégrées pour les ressources Azure

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>Alertes de sauvegarde Azure intégrées pour les ressources On-prem

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>Sources de données actives

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>Stockage cloud du coffre Recovery Services

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Les graphiques ci-dessus sont fournis avec le modèle et le client est libre de les modifier/d’en ajouter.

> [!IMPORTANT]
> Lorsque vous déployez le modèle, il crée en fait un verrou en lecture seule que vous devez supprimer pour modifier et enregistrer le modèle. Pour supprimer des verrous, regardez dans le volet « Verrous » sous la section « Paramètres » de l’espace de travail Log Analytics.

### <a name="create-alerts-using-log-analytics"></a>Créer des alertes avec Log Analytics

Azure Monitor permet aux utilisateurs de créer leurs propres alertes à partir de l’espace de travail LA où vous pouvez *tirer parti des groupes d’actions Azure pour sélectionner votre mécanisme de notification par défaut*. Cliquez sur l’un des graphiques ci-dessus pour ouvrir la section « Journaux » de l’espace de travail LA ***où vous pouvez modifier les demandes et créer des alertes au-dessus d’elles***.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Si vous cliquez sur « Nouvelle règle d’alerte » comme indiqué ci-dessus, l’écran de création d’alertes d’Azure Monitor s’ouvre.

Comme vous pouvez le constater ci-dessous, la ressource est déjà marquée, puisque l’espace de travail LA et l’intégration des groupes d’actions sont fournis.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> Veuillez noter que l’impact pertinent sur la tarification de la création de cette demande est fourni [ici](https://azure.microsoft.com/pricing/details/monitor/).

#### <a name="alert-condition"></a>Condition d’alerte

L’aspect clé est la condition de déclenchement de l’alerte. Si vous cliquez sur « Condition », la requête Kusto est automatiquement chargée dans l’écran « Journaux d'activité » comme indiqué ci-dessous, et vous pouvez le modifier pour l’adapter à votre scénario. Certains exemples de demandes Kusto sont fournis dans la [section ci-dessous](#sample-kusto-queries).

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Modifiez la demande Kusto, si nécessaire, sélectionnez le bon seuil (qui décidera du déclenchement de l’alerte), la bonne période (fenêtre de temps pendant laquelle la requête est exécutée) et la fréquence. Par exemple :  si le seuil est supérieur à 0 et que la période et la fréquence sont respectivement égales à 5 minutes, la règle est alors traduite en tant qu’« Exécuter la requête toutes les 5 minutes pour les 5 dernières minutes et, si le nombre de résultats est supérieur à 0, m’avertir via le groupe d'actions sélectionné ».

#### <a name="action-group-integration"></a>Intégration des groupes d’actions

Les groupes d’actions spécifient les canaux de notification qui sont à la disposition de l’utilisateur. En cliquant sur « Créer » sous « Groupes d’actions », vous affichez la liste des mécanismes de notification disponibles.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

En savoir plus sur les [Alertes à partir de l’espace de travail LA](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) et sur les [Groupes d’actions](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) à partir de la documentation d’Azure Monitor.

Par conséquent, vous pouvez satisfaire à toutes les exigences d’alerte à partir de LA uniquement ou l’utiliser comme technique supplémentaire en plus des mécanismes de notification intégrés.

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

Les graphiques par défaut vous fournissent des requêtes Kusto pour les scénarios de base sur lesquels vous pouvez créer des alertes. Vous pouvez également les modifier pour obtenir les données pour lesquelles vous souhaitez recevoir des alertes. Voici quelques exemples de requêtes Kusto que vous pouvez coller dans la fenêtre « Journaux » avant de créer une alerte sur la requête.

#### <a name="all-successful-backup-jobs"></a>Tous les travaux de sauvegarde réussis

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>Tous les travaux de sauvegarde ayant échoué

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>Tous les travaux de sauvegarde de machines virtuelles Azure réussis

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

#### <a name="all-successful-sql-log-backup-jobs"></a>Tous les travaux de sauvegarde de journal SQL réussis

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

#### <a name="all-successful-mab-agent-backup-jobs"></a>Tous les travaux de sauvegarde d’agent MAB réussis

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

### <a name="diagnostic-data-update-frequency"></a>Fréquence de mise à jour des données de diagnostic

Les données de diagnostic provenant du coffre sont pompées dans l’espace de travail LA avec un décalage. Chaque événement arrive à l’espace de travail LA ***avec un décalage de 20 à 30 minutes après avoir été envoyé par le coffre Recovery Services.***

- Les alertes intégrées du service de sauvegarde (dans toutes les solutions) sont envoyées dès qu’elles sont créées. Ceci signifie qu’elles apparaissent généralement dans l’espace de travail LA au bout de 20 à 30 minutes.
- Les travaux de sauvegarde et de restauration ad hoc (dans toutes les solutions) sont envoyés dès qu’ils ***sont terminés***.
- Les travaux de sauvegarde planifiés à partir de toutes les solutions (à l’exception de la sauvegarde SQL) sont envoyés dès qu’ils ***sont terminés***.
- Pour la sauvegarde SQL, dans la mesure où nous pouvons avoir des sauvegardes de journal toutes les 15 minutes, pour toutes les tâches de sauvegarde planifiées terminées, y compris les journaux, les informations sont traitées par lot et envoyées toutes les 6 heures.
- Toutes les autres informations, concernant par exemple l’élément de sauvegarde, les points de récupération, le stockage, etc. dans toutes les solutions sont envoyées **au moins une fois par jour.**
- Une modification dans la configuration de sauvegarde, par ex., de la stratégie de modification, etc. déclenche un envoi de toutes les informations de sauvegarde correspondantes.

## <a name="using-rs-vaults-activity-logs"></a>Utilisation des journaux d’activité du coffre RS

Vous pouvez également utiliser les journaux d’activité pour obtenir des notifications pour des événements comme une sauvegarde réussie.

> [!CAUTION]
> **Notez que ceci est uniquement applicable aux sauvegardes de machines virtuelles Azure.** Cela n’est pas possible pour d’autres solutions telles que l’agent de sauvegarde Azure, les sauvegardes SQL dans Azure, Azure Files, etc.

### <a name="sign-in-into-azure-portal"></a>Connectez-vous au portail Azure

Connectez-vous au portail Azure, accédez au coffre Azure Recovery Services approprié, puis cliquez sur la section « Journal d’activité » dans les propriétés.

### <a name="identify-appropriate-log-and-create-alert"></a>Identifiez le journal approprié et créez une alerte

Appliquez les filtres de l’image suivante pour vérifier si vous recevez bien les journaux d’activité en cas de sauvegardes réussies. Changez l’intervalle de temps pour afficher les enregistrements.

![Journaux d’activité pour les sauvegardes de machines virtuelles Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

Cliquez sur le nom de l’opération pour afficher l’opération et les détails pertinents.

![Nouvelle règle d’alerte](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Cliquez sur **Nouvelle règle d’alerte** pour ouvrir l’écran **Créer une règle**. Vous pouvez ici créer des alertes en suivant les étapes décrites dans cet [article](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

Ici, la ressource est le coffre Recovery Service proprement dit et, par conséquent, vous devez répéter la même action pour tous les coffres dans lesquels vous souhaitez une notification via les journaux d’activité. La condition n’a ni seuil, ni période, ni fréquence, dans la mesure où il s’agit d’une alerte basée sur les événements. Dès que le journal d’activité pertinent est généré, l’alerte est déclenchée.

## <a name="recommendation"></a>Recommandation

***Toutes les alertes créées à partir des journaux d’activité et des espaces de travail LA peuvent être affichées dans Azure Monitor, dans le volet « Alertes » à gauche.***

Si la notification par le biais de journaux d’activité peut être utilisée, ***le service de sauvegarde Azure recommande vivement d’utiliser LA pour la surveillance à l’échelle et PAS les journaux d’activité, et ce, pour les raisons suivantes***.

- **Scénarios limités :** applicable uniquement pour les sauvegardes de machines virtuelles Azure et à répéter pour chaque coffre Recovery Services.
- **Ajustement de la définition :** l’activité de sauvegarde planifiée ne correspond pas à la définition la plus récente des journaux d’activité et s’aligne sur les [journaux de diagnostic](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs). Ceci entraîne un impact inattendu lorsque le pompage de données via le canal du journal d'activité est modifié comme indiqué ci-dessous.
- **Problèmes avec le canal du journal d’activité :** Nous sommes passés à un nouveau modèle de pompage des journaux d’activité de Sauvegarde Azure sur les coffres Recovery Services. Malheureusement, le déplacement a affecté la génération de journaux d’activité dans des clouds souverains Azure. Si des utilisateurs de clouds souverains Azure ont créé/configuré des alertes à partir de journaux d’activité par le biais d’Azure Monitor, elles ne se déclenchent pas. En outre, dans toutes les régions publiques Azure, si un utilisateur collecte des journaux d’activité Recovery Services dans un espace de travail Log Analytics, comme mentionné [ici](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), ces journaux d’activité n’apparaissent pas non plus.

Par conséquent, il est vivement recommandé d’utiliser espace de travail Log Analytics pour la surveillance et l’alerte à l’échelle de toutes vos charges de travail protégées par la sauvegarde Azure.

## <a name="next-steps"></a>Étapes suivantes

- Reportez-vous au [modèle de données Log Analytics](backup-azure-log-analytics-data-model.md) pour créer des requêtes personnalisées.
