---
title: Gérer et surveiller des bases de données SQL Server sur une machine virtuelle Azure sauvegardée par le service Sauvegarde Azure | Microsoft Docs
description: Cet article explique comment restaurer des bases de données SQL Server exécutées sur une machine virtuelle Azure et sauvegardées avec Sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430953"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gérer et surveiller des bases de données SQL Server sauvegardées 


Cet article décrit les tâches courantes de gestion et de surveillance des bases de données SQL Server exécutées sur une machine virtuelle Azure et sauvegardées dans un coffre Azure Backup Recovery Services par le service [Sauvegarde Azure](backup-overview.md). Tâches incluant la surveillance des travaux et des alertes, l'arrêt et la reprise de la protection des bases de données, l'exécution des travaux de sauvegarde et l'annulation de l'inscription à la sauvegarde d'une machine virtuelle.


> [!NOTE]
> La sauvegarde des bases de données SQL Server exécutées sur une machine virtuelle Azure avec le service Sauvegarde Azure est actuellement disponible en préversion publique.


Si vous n'avez pas encore configuré la sauvegarde pour les bases de données SQL Server, suivez les instructions fournies dans [cet article](backup-azure-sql-database.md).

## <a name="monitor-backup-jobs"></a>Surveiller les travaux de sauvegarde

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>Surveiller les travaux ad hoc sur le portail

Le service Sauvegarde Azure affiche tous les travaux déclenchés manuellement sur le portail **​​Travaux de sauvegarde**, y compris la détection et l'inscription des bases de données, ainsi que les opérations de sauvegarde et de restauration.

![Portail des Travaux de sauvegarde](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Les travaux de sauvegarde planifiés n'apparaissent pas sur le portail **​​Travaux de sauvegarde**. Utilisez SQL Server Management Studio pour surveiller les travaux de sauvegarde planifiés, comme décrit dans la section suivante.
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>Surveiller les travaux de sauvegarde à l'aide de SQL Server Management Studio 

Le service Sauvegarde Azure utilise des API natives de SQL pour toutes les opérations de sauvegarde.

Utilisez les API natives pour extraire toutes les informations des travaux à partir de la [table backupset SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) de la base de données msdb.

L’exemple suivant est une requête permettant d’extraire tous les travaux de sauvegarde pour la base de données nommée **DB1**. Personnalisez la requête pour une surveillance plus poussée.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>Afficher les alertes de sauvegarde

Comme les sauvegardes de fichiers journaux interviennent toutes les 15 minutes, la surveillance des tâches de sauvegarde peut s'avérer fastidieuse. Le service Sauvegarde Azure facilite la surveillance avec des alertes par e-mail.

- Des alertes sont déclenchées pour tous les échecs de sauvegarde.
- Les alertes sont consolidées au niveau de la base de données par code d’erreur.
- Une alerte par e-mail est envoyée uniquement pour le premier échec de sauvegarde pour une base de données. 

Pour surveiller les alertes de sauvegarde :

1. Pour surveiller les alertes de sauvegarde, connectez-vous à votre abonnement Azure sur le [portail Azure](https://portal.azure.com).

2. Sur le tableau de bord du coffre, sélectionnez **Alertes et événements**.

   ![Sélectionner Alertes et événements](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Dans **Alertes et événements**, sélectionnez **Alertes de sauvegarde**.

   ![Sélectionner Alertes de sauvegarde](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Arrêter la protection d’une base de données SQL Server

Pour arrêter la sauvegarde d'une base de données SQL Server, deux méthodes sont disponibles :

* Arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération.
* Arrêter tous les travaux de sauvegarde à venir en conservant les points de récupération.

Notez les points suivants :

Si vous conservez les points de récupération, ceux-ci seront nettoyés conformément à la stratégie de sauvegarde. Vous paierez des frais pour l'instance protégée et le volume de stockage utilisé jusqu'à ce que tous les points de récupération soient nettoyés. [Apprenez-en davantage sur la](https://azure.microsoft.com/pricing/details/backup/) tarification.
- Lorsque vous conservez les points de récupération, même s'ils expirent conformément à la stratégie de rétention, le service Sauvegarde Azure conserve toujours un dernier point de récupération jusqu'à ce que vous supprimiez explicitement les données de sauvegarde.
- Si vous supprimez une source de données sans arrêter la sauvegarde, les nouvelles sauvegardes commenceront à échouer. Là encore, les anciens points de récupération expireront conformément à la stratégie, mais un dernier point de récupération sera conservé jusqu'à ce que vous arrêtiez la sauvegarde et supprimiez les données.
- Vous ne pouvez pas arrêter la sauvegarde d'une base de données pour laquelle la protection automatique est activée tant que la protection automatique n'est pas désactivée.

Pour arrêter la protection de la base de données :

1. Dans le tableau de bord du coffre, sous **Utilisation**, sélectionnez **Éléments de sauvegarde**.

    ![Ouvrir le menu Éléments de sauvegarde](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

2. Dans **Type de gestion des sauvegardes**, sélectionnez **SQL dans une machine virtuelle Azure**.

    ![Sélectionner SQL in Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. Sélectionnez la base de données dont vous souhaitez arrêter la protection.

    ![Sélectionner la protection de la base de données a arrêter](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. Dans le menu de la base de données, sélectionnez **Arrêter la sauvegarde**.

    ![Sélectionner Arrêter la sauvegarde](./media/backup-azure-sql-database/stop-db-button.png)


6. Dans le menu **Arrêter la sauvegarde**, indiquez si vous souhaitez conserver ou supprimer les données. Vous pouvez, si vous le souhaitez, indiquer une raison ou un commentaire.

    ![Menu Arrêter la sauvegarde](./media/backup-azure-sql-database/stop-backup-button.png)

7. Cliquez sur **Arrêter la sauvegarde**.

  

### <a name="resume-protection-for-a-sql-database"></a>Rétablir la protection d’une base de données SQL

Si vous avez sélectionné l’option **Conserver les données de sauvegarde** au moment de l’arrêt de la protection de la base de données SQL, vous avez la possibilité de rétablir la protection. Si les données de sauvegarde n’ont pas été conservées, la protection ne peut pas reprendre.

1. Pour rétablir la protection de la base de données SQL, ouvrez l’élément de sauvegarde, puis cliquez sur **Reprendre la sauvegarde**.

    ![Sélectionner Reprendre la sauvegarde pour relancer la protection de la base de données](./media/backup-azure-sql-database/resume-backup-button.png)

2. Dans le menu **Stratégie de sauvegarde**, sélectionnez une stratégie, puis sélectionnez **Enregistrer**.

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Vous pouvez exécuter différents types de sauvegardes à la demande :

* Sauvegarde complète
* Sauvegarde de copie uniquement
* Sauvegarde différentielle
* Sauvegarde du journal

[Découvrez-en plus](backup-architecture.md#sql-server-backup-types) sur les types de sauvegarde SQL Server.

## <a name="unregister-a-sql-server-instance"></a>Supprimer l'inscription d'une instance SQL Server

Annulez l'inscription d'une instance de SQL Server après avoir désactivé la protection, mais avant de supprimer le coffre :

1. Sur le tableau de bord du coffre, sous **Gestion**, sélectionnez **Infrastructure de sauvegarde**.  

   ![Choisir Infrastructure de sauvegarde](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Sous **Management Servers** (Serveurs d’administration), sélectionnez **Protected Servers** (serveurs protégés).

   ![Sélectionner Serveurs protégés](./media/backup-azure-sql-database/protected-servers.png)


3. Dans **Serveurs protégés**, sélectionnez le serveur dont vous souhaitez annuler l'inscription. Si vous souhaitez supprimer le coffre, vous devez annuler l’inscription de tous les serveurs.

4. Cliquez avec le bouton droit sur le serveur protégé > **Supprimer**.

   ![Sélectionner Supprimer](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>Étapes suivantes

[Consultez](backup-sql-server-azure-troubleshoot.md) les informations de dépannage relatives à la sauvegarde des bases de données SQL Server.
