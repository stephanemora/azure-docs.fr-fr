---
title: surveiller la sauvegarde Azure avec Azure Monitor
description: Supervisez les charges de travail de Sauvegarde Azure et créez des alertes personnalisées avec Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 983939a905c6c096f2e8e3007bd40cbbe9088395
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611694"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Superviser à grande échelle avec Azure Monitor

Sauvegarde Azure fournit [des fonctionnalités intégrées de supervision et d’alerte](backup-azure-monitoring-built-in-monitor.md) dans un coffre Recovery Services. Ces fonctionnalités sont disponibles sans infrastructure de gestion supplémentaire. Ce service intégré est cependant limité aux scénarios suivants :

- Si vous supervisez des données de plusieurs coffres Recovery Services sur différents abonnements
- Si le canal de notification favori n’est *pas* l’e-mail
- Si les utilisateurs veulent des alertes pour d’autres scénarios
- Si vous voulez voir des informations provenant de composants locaux comme System Center Data Protection Manager (SC-DPM) dans Azure qui ne sont pas visibles dans [**Travaux de sauvegarde**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) ou dans [**Alertes de sauvegarde**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Utilisation d’un espace de travail Log Analytics

> [!NOTE]
> Des données provenant de sauvegardes de machines virtuelles Azure, de l’agent Sauvegarde Azure, de System Center Data Protection Manager, de sauvegardes SQL dans des machines virtuelles Azure et de sauvegardes de partage de fichiers Azure sont injectées dans l’espace de travail Log Analytics via des paramètres de diagnostic. La prise en charge du serveur de sauvegarde Microsoft Azure (MABS, Microsoft Azure Backup Server) sera bientôt disponible.

Pour superviser/générer des rapports à grande échelle, vous avez besoin des fonctionnalités de deux services Azure. *Les paramètres de diagnostic* envoient des données provenant de plusieurs ressources Azure Resource Manager vers une autre ressource. *Log Analytics* génère des alertes personnalisées où vous pouvez utiliser des groupes d’actions pour définir d’autres canaux de notification.

Les sections suivantes expliquent comment utiliser Log Analytics pour superviser Sauvegarde Azure à grande échelle.

### <a name="configure-diagnostic-settings"></a>Configurer les paramètres de diagnostic

Des ressources Azure Resource Manager comme le coffre Recovery Services, enregistrent des informations sur les opérations planifiées et sur les opérations déclenchées par l’utilisateur au titre de données de diagnostic.

Dans la section Supervision, sélectionnez **Paramètres de diagnostic** et spécifiez la cible pour les données de diagnostic du coffre Recovery Services.

![Paramètre de diagnostic du coffre Recovery Services, ciblant Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Vous pouvez cibler un espace de travail Log Analytics depuis un autre abonnement. Pour superviser des coffres sur plusieurs abonnements à un même emplacement, sélectionnez le même espace de travail Log Analytics pour plusieurs coffres Recovery Services. Pour canaliser toutes les informations relatives à Sauvegarde Azure vers l’espace de travail Log Analytics, choisissez **AzureDiagnostics** sur le bouton bascule qui s’affiche, puis sélectionnez l’événement **AzureBackupReport**.

> [!IMPORTANT]
> Une fois la configuration terminée, vous devez attendre 24 heures pour que l’envoi (push) de données initial se termine. Après cet envoi (push) de données initial, tous les événements sont envoyés comme décrit plus loin dans cet article, dans la section [Fréquence](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Déployer une solution sur l’espace de travail Log Analytics

> [!IMPORTANT]
> Nous avons publié un [modèle](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) multivue mis à jour pour la supervision et la création de rapports basés sur Log Analytics dans Sauvegarde Azure. Notez que les utilisateurs qui utilisaient la [solution antérieure](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) continuent à la voir dans leurs espaces de travail, même après le déploiement de la nouvelle solution. Toutefois, l’ancienne solution peut fournir des résultats incorrects en raison de modifications mineures du schéma. Les utilisateurs doivent donc déployer le nouveau modèle.

Une fois que les données sont dans l’espace de travail Log Analytics, [déployez un modèle GitHub](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) sur Log Analytics pour visualiser les données. Pour identifier correctement l’espace de travail, veillez à lui donner le même groupe de ressources, le même nom d’espace de travail et le même emplacement d’espace de travail. Installez ensuite ce modèle sur l’espace de travail.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Afficher des données de Sauvegarde Azure avec Log Analytics

- **Azure Monitor** : Dans la section **Insights**, sélectionnez **Plus**, puis choisissez l’espace de travail approprié.
- **Espaces de travail Log Analytics** : Sélectionnez l’espace de travail approprié puis, sous **Général**, sélectionnez **Récapitulatif de l’espace de travail**.

![Vignettes de supervision et de création de rapports Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-overview-dashboard.png)

Lorsque vous sélectionnez l’une des vignettes de vue d’ensemble, vous pouvez afficher des informations supplémentaires. Voici quelques-uns des rapports qui s’affichent :

- Travaux de sauvegarde (sans journal)

   ![Graphiques Log Analytics pour les travaux de sauvegarde](media/backup-azure-monitoring-laworkspace/la-azurebackup-backupjobsnonlog.png)

- Alertes à partir de la sauvegarde des ressources Azure

   ![Graphiques Log Analytics pour les travaux de restauration](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertsazure.png)

De même, en cliquant sur les autres vignettes, vous pouvez voir des rapports sur les travaux de restauration, le stockage cloud, les éléments de sauvegarde, les alertes provenant de la sauvegarde des ressources locales et les travaux de sauvegarde de journal.

Ces graphiques sont fournis avec le modèle. Vous pouvez modifier les graphiques ou en ajouter si nécessaire.

### <a name="create-alerts-by-using-log-analytics"></a>Créer des alertes avec Log Analytics

Dans Azure Monitor, vous pouvez créer vos propres alertes dans un espace de travail Log Analytics. Dans l’espace de travail, vous utilisez des *groupes d’actions Azure* pour sélectionner votre mécanisme de notification préféré.

> [!IMPORTANT]
> Pour plus d’informations sur le coût de création de cette requête, consultez [Tarification d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Sélectionnez un des graphiques pour ouvrir la section **Journaux** de l’espace de travail Log Analytics. Dans la section **Journaux**, modifiez les requêtes et créez des alertes sur celles-ci.

![Créer une alerte dans un espace de travail Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Quand vous sélectionnez **Nouvelle règle d’alerte**, la page de création d’alerte Azure Monitor s’ouvre, comme illustré dans l’image suivante. Ici, la ressource est déjà marquée, car l’espace de travail Log Analytics et l’intégration des groupes d’actions sont fournis.

![Page de création d’alerte Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Condition d’alerte

La caractéristique définissant une alerte est sa condition de déclenchement. Sélectionnez **Condition** pour charger automatiquement la requête Kusto dans la page **Journaux**, comme illustré dans l’image suivante. Ici, vous pouvez modifier la condition pour l’adapter à vos besoins. Pour plus d’informations, consultez [Exemples de requêtes Kusto](#sample-kusto-queries).

![Configuration d’une condition d’alerte](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Si nécessaire, vous pouvez modifier la requête Kusto. Choisissez un seuil, une période et une fréquence. Le seuil détermine à quel moment l’alerte sera déclenchée. La période correspond à la fenêtre de temps pendant laquelle la requête est exécutée. Par exemple, si le seuil est supérieur à 0, la période est de 5 minutes et la fréquence est de 5 minutes, la règle exécute la requête toutes les 5 minutes, en examinant les 5 minutes précédentes. Si le nombre de résultats est supérieur à 0, vous êtes averti via le groupe d’actions sélectionné.

#### <a name="alert-action-groups"></a>Groupes d’actions d’alerte

Utilisez un groupe d’actions pour spécifier un canal de notification. Pour voir les mécanismes de notification disponibles, sous **Groupes d’actions**, sélectionnez **Créer**.

![Mécanismes de notification disponibles dans la fenêtre « Ajouter un groupe d’actions »](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Vous pouvez satisfaire à toutes les exigences en matière d’alertes et de supervision à partir de Log Analytics seul, ou vous pouvez utiliser Log Analytics pour compléter les notifications intégrées.

Pour plus d’informations, consultez [Créer, afficher et gérer les alertes de journal avec Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) et [Créer et gérer des groupes d’actions dans le portail Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

Les graphiques par défaut vous fournissent des requêtes Kusto pour les scénarios de base sur lesquels vous pouvez créer des alertes. Vous pouvez également modifier les requêtes pour obtenir les données pour lesquelles vous voulez recevoir des alertes. Collez les exemples de requêtes Kusto suivants dans la page **Journaux**, puis créez des alertes sur les requêtes :

- Tous les travaux de sauvegarde réussis

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Tous les travaux de sauvegarde ayant échoué

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Tous les travaux de sauvegarde de machines virtuelles Azure réussis

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

- Tous les travaux de sauvegarde de journal SQL réussis

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

- Tous les travaux de l’agent Sauvegarde Azure réussis

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

Les données de diagnostic provenant du coffre sont injectées dans l’espace de travail Log Analytics avec un certain décalage. Chaque événement arrive dans l’espace de travail Log Analytics *20 à 30 minutes* après son envoi (push) depuis le coffre Recovery Services. Voici plus d’informations sur le décalage :

- Dans toutes les solutions, les alertes intégrées du service de sauvegarde sont envoyées (push) dès qu’elles sont créées. Elles apparaissent donc généralement dans l’espace de travail Log Analytics après 20 à 30 minutes.
- Dans toutes les solutions, les travaux de sauvegarde et les travaux de restauration à la demande sont envoyés (push) dès qu’ils se *terminent*.
- Pour toutes les solutions, à l’exception de la sauvegarde SQL, les travaux de sauvegarde planifiés sont envoyés (push) dès qu’ils se *terminent*.
- Pour la sauvegarde SQL, comme des sauvegardes de journal peuvent se produire toutes les 15 minutes, les informations pour tous les travaux de sauvegarde planifiés terminés, y compris les journaux, sont traitées par lot et envoyées (push) toutes les 6 heures.
- Dans toutes les solutions, d’autres informations, comme l’élément de sauvegarde, la stratégie de sauvegarde, les points de récupération de sauvegarde, le stockage de sauvegarde, etc., sont envoyées (par push) au moins *une fois par jour*.
- Une modification dans la configuration de sauvegarde (comme la stratégie de changement ou de modification) déclenche un envoi (push) de toutes les informations de sauvegarde associées.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Utilisation des journaux d’activité du coffre Recovery Services

> [!CAUTION]
> Les étapes suivantes s’appliquent seulement aux *sauvegardes de machines virtuelles Azure*. Vous ne pouvez pas utiliser ces étapes pour des solutions comme l’agent Sauvegarde Azure, les sauvegardes SQL dans Azure ou Azure Files.

Vous pouvez aussi utiliser des journaux d’activité pour obtenir des notifications pour des événements comme la réussite des sauvegardes. Pour commencer, suivez ces étapes :

1. Connectez-vous au portail Azure.
1. Ouvrez le coffre Recovery Services approprié.
1. Dans les propriétés du coffre, ouvrez la section **Journal d’activité**.

Pour identifier le journal approprié et créer une alerte :

1. Vérifiez que vous recevez bien les journaux d’activité pour les sauvegardes réussies en appliquant les filtres montrés dans l’image suivante. Modifiez la valeur de **Intervalle de temps** selon les besoins pour afficher les enregistrements.

   ![Filtrage pour rechercher des journaux d’activité pour les sauvegardes de machines virtuelles Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Sélectionnez le nom de l’opération pour voir les détails correspondants.
1. Sélectionnez **Nouvelle règle d’alerte** pour ouvrir la page **Créer une règle**.
1. Créez une alerte en suivant les étapes de [Créer, afficher et gérer des alertes de journal d’activité avec Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nouvelle règle d’alerte](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Ici, la ressource est le coffre Recovery Services lui-même. Répétez les mêmes étapes pour tous les coffres pour lesquels vous voulez recevoir des notifications par le biais des journaux d’activité. La condition n’a pas de seuil, de période ou de fréquence, car cette alerte est basée sur des événements. Dès que le journal d’activité approprié est généré, l’alerte est déclenchée.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Utilisation de Log Analytics pour superviser à grande échelle

Vous pouvez voir toutes les alertes créées à partir des journaux d’activité et des espaces de travail Log Analytics dans Azure Monitor. Ouvrez simplement le volet **Alertes** sur la gauche.

Même si vous pouvez recevoir des notifications via des journaux d’activité, nous vous recommandons vivement d’utiliser Log Analytics plutôt que des journaux d’activité pour la supervision à grande échelle. Voici pourquoi :

- **Scénarios limités** : Les notifications via des journaux d’activité s’appliquent seulement aux sauvegardes de machines virtuelles Azure. Les notifications doivent être configurées pour chaque coffre Recovery Services.
- **Ajustement de la définition** : L’activité de sauvegarde planifiée ne correspond pas à la définition la plus récente des journaux d’activité. Au lieu de cela, elle s’aligne sur les [journaux de ressources](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace). Cet alignement entraîne des effets inattendus quand les données qui transitent via le canal du journal d’activité changent.
- **Problèmes avec le canal du journal d’activité** : Dans les coffres Recovery Services, les journaux d’activité qui sont injectés depuis Sauvegarde Azure suivent un nouveau modèle. Malheureusement, ce changement affecte la génération des journaux d’activité dans Azure Government, Azure Allemagne et Azure Chine 21Vianet. Si les utilisateurs de ces services cloud créent ou configurent des alertes à partir de journaux d’activité dans Azure Monitor, les alertes ne sont pas déclenchées. De plus, dans toutes les régions publiques Azure, si un utilisateur [collecte des journaux d’activité Recovery Services dans un espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), ces journaux n’apparaissent pas.

Utilisez un espace de travail Log Analytics pour la supervision et la génération d’alertes à grande échelle pour toutes vos charges de travail protégées par Sauvegarde Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour créer des requêtes personnalisées, consultez [Modèle de données Log Analytics](backup-azure-log-analytics-data-model.md).
