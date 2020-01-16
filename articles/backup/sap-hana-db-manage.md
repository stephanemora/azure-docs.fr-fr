---
title: Gérer les bases de données SAP HANA sauvegardées sur des machines virtuelles Azure
description: Dans cet article, découvrez les tâches courantes de gestion et de supervision des bases de données SAP HANA qui s’exécutent sur des machines virtuelles Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: a9462f8608fc5ae35255ac321a0742b3f1834fde
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390617"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Gérer et superviser des bases de données SAP HANA sauvegardées

Cet article décrit les tâches courantes de gestion et de supervision des bases de données SAP HANA s’exécutant sur une machine virtuelle Azure et sauvegardées dans un coffre Recovery Services par le service [Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-overview). Vous apprendrez à superviser des travaux et des alertes, déclencher une sauvegarde à la demande, modifier des stratégies, arrêter et reprendre la protection de la base de données, ainsi que désinscrire une machine virtuelle des sauvegardes.

Si vous n’avez pas encore configuré de sauvegardes pour vos bases de données SAP HANA, consultez [Sauvegarder des bases de données SAP HANA sur des machines virtuelles Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Surveiller les travaux de sauvegarde manuelles sur le portail

Sauvegarde Azure affiche tous les travaux déclenchés manuellement dans la section **Travaux de sauvegarde** sur le Portail Azure.

![Section Travaux de sauvegarde](./media/sap-hana-db-manage/backup-jobs.png)

Les travaux que vous voyez sur ce portail incluent les opérations de découverte, d’inscription, de sauvegarde et de restauration de base de données. Les travaux planifiés, notamment les sauvegardes de fichiers journaux, n’apparaissent pas dans cette section. Les sauvegardes déclenchées manuellement à partir des clients natifs SAP HANA (Studio/Cockpit/DBA Cockpit) n’apparaissent pas non plus ici.

![Liste des travaux de sauvegarde](./media/sap-hana-db-manage/backup-jobs-list.png)

Pour en savoir plus sur la supervision, consultez [Supervision dans le Portail Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) et [Supervision à l’aide d’Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="view-backup-alerts"></a>Afficher les alertes de sauvegarde

Les alertes sont un moyen simple de superviser les sauvegardes des bases de données SAP HANA. Elles vous aident à vous concentrer sur les événements qui vous intéressent le plus sans vous perdre dans la multitude d’événements qu'une sauvegarde génère. Sauvegarde Azure vous permet de définir des alertes, et ces dernières peuvent être supervisées comme suit :

* Connectez-vous au [portail Azure](https://portal.azure.com/).
* Dans le tableau de bord du coffre, sélectionnez **Alertes de sauvegarde**.

  ![Alertes de sauvegarde sur le tableau de bord du coffre](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Vous pourrez voir les alertes :

  ![Liste des alertes de sauvegarde](./media/sap-hana-db-manage/backup-alerts-list.png)

* Cliquez sur les alertes pour afficher plus de détails :

  ![Détails de l’alerte](./media/sap-hana-db-manage/alert-details.png)

Aujourd’hui, Sauvegarde Azure permet d’envoyer des alertes par e-mail. Ces alertes sont les suivantes :

* Déclenchées pour tous les échecs de sauvegarde.
* Consolidées au niveau de la base de données par code d’erreur.
* Envoyées uniquement pour le premier échec de sauvegarde d’une base de données.

Pour en savoir plus sur la supervision, consultez [Supervision dans le Portail Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) et [Supervision à l’aide d’Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

## <a name="management-operations"></a>Opérations de gestion

Sauvegarde Azure simplifie la gestion d’une base de données SAP HANA sauvegardée grâce à une multitude d’opérations de gestion prises en charge. Ces opérations sont décrites plus en détail dans les sections suivantes.

### <a name="run-an-ad-hoc-backup"></a>Exécuter une sauvegarde ad hoc

Les sauvegardes s’exécutent conformément à la planification de la stratégie. Vous pouvez exécuter une sauvegarde à la demande en procédant comme suit :

1. Dans le menu du coffre, cliquez sur **Éléments de sauvegarde**.
2. Dans **Éléments de sauvegarde**, sélectionnez la machine virtuelle exécutant la base de données SAP HANA, puis cliquez sur **Sauvegarder maintenant**.
3. Dans **Sauvegarder maintenant**, utilisez le contrôle de calendrier pour sélectionner le dernier jour de rétention du point de récupération. Cliquez ensuite sur **OK**.
4. Surveiller les notifications du portail. Vous pouvez surveiller la progression du travail dans le tableau de bord du coffre > **Travaux de sauvegarde** > **En cours d’exécution**. Selon la taille de votre base de données, la création de la sauvegarde initiale peut prendre un certain temps.

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Exécuter une sauvegarde de client natif SAP HANA sur une base de données pour laquelle la sauvegarde Azure est activée

Si vous souhaitez sauvegarder localement (à l’aide de HANA Studio/Cockpit) une base de données qui est sauvegardée par Sauvegarde Azure, procédez comme suit :

1. Attendez la fin de toute sauvegarde complète ou de fichier journal de la base de données. Vérifiez l’état dans SAP HANA Studio/Cockpit.
2. Désactivez les sauvegardes de fichiers journaux, puis définissez le catalogue de sauvegarde sur le système de fichiers pour la base de données appropriée.
3. Pour ce faire, double-cliquez sur **systemdb** > **Configuration (Configuration)**  > **Select Database (Sélectionner la base de données)**  > **Filter (Log) (Filtrer [journal])** .
4. Définissez **enable_auto_log_backup** sur **No (Non)** .
5. Définissez **log_backup_using_backint** sur **False (Faux)** .
6. Effectuez une sauvegarde complète à la demande de la base de données.
7. Attendez la fin de la sauvegarde complète et de la sauvegarde du catalogue.
8. Rétablissez les paramètres précédents sur les valeurs pour Azure :
   * Définissez **enable_auto_log_backup** sur **Yes (Oui)** .
   * Définissez **log_backup_using_backint** sur **True (Vrai)** .

### <a name="change-policy"></a>Changer la stratégie

Vous pouvez changer la stratégie sous-jacente d’un élément de sauvegarde SAP HANA.

* Dans le tableau de bord du coffre, accédez à **Éléments de sauvegarde** :

  ![Sélectionner les éléments de sauvegarde](./media/sap-hana-db-manage/backup-items.png)

* Choisissez **SAP HANA dans les machines virtuelles Azure**

  ![Choisir SAP HANA dans les machines virtuelles Azure](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Choisissez l’élément de sauvegarde dont vous souhaitez changer la stratégie sous-jacente.
* Cliquez sur la stratégie de sauvegarde existante.

  ![Sélectionnez une stratégie de sauvegarde existante.](./media/sap-hana-db-manage/existing-backup-policy.png)

* Changez la stratégie en choisissant dans la liste. [Créez une nouvelle stratégie de sauvegarde](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy) si nécessaire.

  ![Choisir la stratégie dans la liste déroulante](./media/sap-hana-db-manage/choose-backup-policy.png)

* Enregistrez les modifications

  ![Enregistrez les modifications](./media/sap-hana-db-manage/save-changes.png)

* La modification de la stratégie aura un impact sur tous les éléments de sauvegarde associés et déclenchera les tâches de **configuration de la protection** correspondantes.

>[!NOTE]
> Toute modification de la période de rétention sera appliquée rétroactivement à tous les anciens points de récupération ainsi qu’aux nouveaux.
>
> Les stratégies de sauvegarde incrémentielle ne peuvent pas être utilisées pour les bases de données SAP HANA. La sauvegarde incrémentielle n’est actuellement pas prise en charge pour ces bases de données.

### <a name="stop-protection-for-an-sap-hana-database"></a>Arrêter la protection d’une base de données SAP HANA

Vous pouvez arrêter la protection d’une base de données SAP HANA de deux manières :

* Arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération.
* Arrêter tous les travaux de sauvegarde à venir, mais conserver les points de récupération.

Si vous choisissez de conserver les points de récupération, gardez à l’esprit les considérations suivantes :

* Tous les points de récupération resteront intacts, toutes les opérations de nettoyage s’arrêteront à l’arrêt de la protection avec données conservées.
* Vous serez facturé pour l’instance protégée et le stockage utilisé. Pour plus d’informations, voir [Tarification Sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).
* Si vous supprimez une source de données sans arrêter les sauvegardes, les nouvelles sauvegardes échouent.

Pour arrêter la protection de la base de données :

* Dans le tableau de bord du coffre, sélectionnez **Éléments de la sauvegarde**.
* Sous **Type de gestion des sauvegardes**, sélectionnez **SAP HANA dans une machine virtuelle Azure**

  ![Sélectionnez SAP HANA dans la machine virtuelle Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Sélectionnez la base de données dont vous souhaitez arrêter la protection :

  ![Sélectionnez la base de données pour arrêter la protection](./media/sap-hana-db-manage/select-database.png)

* Dans le menu de la base de données, sélectionnez **Arrêter la sauvegarde**.

  ![Sélectionnez Arrêter la sauvegarde](./media/sap-hana-db-manage/stop-backup.png)

* Dans le menu **Arrêter la sauvegarde**, indiquez si vous souhaitez conserver ou supprimer les données. Vous pouvez également indiquer une raison ou formuler un commentaire.

  ![Sélectionnez Conserver ou supprimer les données](./media/sap-hana-db-manage/retain-backup-data.png)

* Sélectionnez **Arrêter la sauvegarde**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Reprendre la protection d’une base de données SAP HANA

Lorsque vous arrêtez la protection de la base de données SAP HANA, si vous sélectionnez l’option **Conserver les données de sauvegarde**, vous pourrez reprendre la protection ultérieurement. Si vous ne conservez pas les données sauvegardées, vous ne pourrez pas reprendre la protection.

Pour reprendre la protection d’une base de données SAP HANA :

* Ouvrez l’élément de la sauvegarde, puis sélectionnez **Reprendre la sauvegarde**.

   ![Sélectionnez Reprendre la sauvegarde](./media/sap-hana-db-manage/resume-backup.png)

* Dans le menu **Stratégie de sauvegarde**, sélectionnez une stratégie, puis sélectionnez **Enregistrer**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Mise à niveau de SAP HANA 1.0 vers 2.0

Découvrez comment continuer la sauvegarde d’une base de données SAP HANA [après la mise à niveau de SAP HANA 1.0 vers 2.0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Mise à niveau sans modification du SID

Découvrez comment continuer la sauvegarde d’une base de données SAP HANA dont [le SID n’a pas été modifié après la mise à niveau](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-database"></a>Désinscrire une base de données SAP HANA

Désinscrivez une instance SAP HANA après avoir désactivé la protection, mais avant de supprimer le coffre :

* Sur le tableau de bord du coffre, sous **Gestion**, sélectionnez **Infrastructure de sauvegarde**.

   ![Choisir Infrastructure de sauvegarde](./media/sap-hana-db-manage/backup-infrastructure.png)

* Sélectionnez le **Type de gestion des sauvegardes** comme **Charge de travail dans une machine virtuelle Azure**

   ![Sélectionnez le Type de gestion des sauvegardes comme Charge de travail dans une machine virtuelle Azure](./media/sap-hana-db-manage/backup-management-type.png)

* Dans **Serveurs protégés**, sélectionnez l’instance à désinscrire. Pour supprimer le coffre, vous devez désinscrire tous les serveurs et toutes les instances.

* Cliquez avec le bouton droit sur l’instance protégée, puis sélectionnez **Désinscrire**.

   ![Sélectionnez désinscrire](./media/sap-hana-db-manage/unregister.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [résoudre les problèmes courants lors de la sauvegarde de bases de données SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot).
