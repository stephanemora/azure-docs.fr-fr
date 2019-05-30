---
title: Utiliser Azure Backup pour restaurer les bases de données SQL Server sauvegardées sur une machine virtuelle Azure | Microsoft Docs
description: Cet article explique comment restaurer des bases de données SQL Server qui s’exécutent sur une machine virtuelle Azure et qui sont sauvegardés avec sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: raynew
ms.openlocfilehash: d8ade598e4f1b6331367e8bd04ad59951ef5de8f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242372"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurer des bases de données SQL Server sur des machines virtuelles Azure

Cet article explique comment restaurer une base de données SQL Server qui s’exécute sur une machine virtuelle (VM) qui le [sauvegarde Azure](backup-overview.md) service a été sauvegardée dans un coffre Azure Backup Recovery Services.

Cet article explique comment restaurer des bases de données SQL Server. Pour plus d’informations, consultez [sauvegarde des bases de données SQL Server sur des machines virtuelles Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Restaurer à un moment ou un point de récupération

Sauvegarde Azure peut restaurer des bases de données SQL Server qui sont en cours d’exécution sur des machines virtuelles Azure comme suit :

- Restaurer à une date ou heure spécifique (à la seconde) à l’aide de sauvegardes de fichier journal. Azure Backup détermine automatiquement la sauvegarde différentielle complète appropriée et la chaîne de sauvegardes de journaux nécessaires à la restauration basée sur l’heure sélectionnée.
- Restaurer une sauvegarde complète ou différentielle spécifique à restaurer à un point de récupération spécifique.


## <a name="prerequisites"></a>Conditions préalables

Avant de restaurer une base de données, notez les points suivants :

- Vous pouvez restaurer la base de données vers une instance SQL Server dans la même région Azure.
- Le serveur de destination doit être inscrit auprès du même coffre que la source.
- Pour restaurer une base de données chiffré de chiffrement transparent des données vers un autre serveur SQL, vous devez d’abord [restaurer le certificat sur le serveur de destination](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Avant de restaurer la base de données « master », démarrez l’instance de SQL Server en mode mono-utilisateur à l’aide de l’option de démarrage **-m AzureWorkloadBackup**.
    - La valeur de **-m** est le nom du client.
    - Seul le nom de client spécifié peut ouvrir la connexion.
- Pour toutes les bases de données système (modèle, master, msdb), arrêtez le service de l’Agent SQL Server avant de déclencher la restauration.
- Fermez toutes les applications qui pourraient essayer d’effectuer une connexion à un de ces bases de données.
- Si vous avez plusieurs instances en cours d’exécution sur un serveur, toutes les instances doivent être augmenté et en cours d’exécution dans le cas contraire le serveur n’apparaît pas dans la liste des serveurs de destination pour vous permettent de restaurer la base de données.

## <a name="restore-a-database"></a>Restauration d’une base de données

Pour restaurer, vous devez disposer des autorisations suivantes :

* **Opérateur de sauvegarde** autorisations dans le coffre dans lequel vous effectuez la restauration.
* L’accès **Contributeur (écriture)** à la machine virtuelle source qui est sauvegardée.
* L’accès **Contributeur (écriture)** à la machine virtuelle cible :
    - Si vous restaurez à la même machine virtuelle, il s’agit de la machine virtuelle de la source.
    - Si vous restaurez vers un autre emplacement, cela est la cible de machine virtuelle.

Restaurez comme ceci :
1. Ouvrez le coffre dans lequel la machine virtuelle SQL Server est inscrite.
2. Dans le tableau de bord du coffre, sous **Utilisation**, sélectionnez **Éléments de sauvegarde**.
3. Dans **Éléments de sauvegarde**, sous **Type de gestion des sauvegardes**, sélectionnez **SQL dans une machine virtuelle Azure**.

    ![Sélectionner SQL in Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Sélectionnez la base de données à restaurer.

    ![Sélectionner la base de données à restaurer](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Passez en revue le menu de la base de données. Il fournit des informations sur la sauvegarde de la base de données, notamment :

    * Le point de restauration le plus ancien et le point de restauration le plus récent.
    * L’état de sauvegarde de journal pour les dernières 24 heures pour les bases de données qui se trouvent dans le mode de récupération full et bulk-logged et qui sont configurés pour les sauvegardes du journal des transactions.

6. Sélectionnez **Restore DB**.

    ![Sélectionner Restore DB](./media/backup-azure-sql-database/restore-db-button.png)

7. Dans **restaurer la Configuration de**, spécifier où restaurer les données :
   - **Autre emplacement** : Restaurer la base de données vers un autre emplacement et conserver la base de données source d’origine.
   - **Remplacer la base de données** : restaure les données dans la même instance SQL Server que la source d’origine. Cette option remplace la base de données d’origine.

     > [!Important]
     > Si la base de données sélectionnée fait partie d’un groupe de disponibilité Always On, SQL Server n’autorise pas le remplacement de la base de données. Seul **Autre emplacement** est disponible.
     >

     ![Menu Configuration de la restauration](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Restaurer à un autre emplacement

1. Dans le **restaurer la Configuration de** menu, sous **où effectuer la restauration**, sélectionnez **autre emplacement**.
2. Sélectionnez le nom du serveur et l’instance SQL Server où vous voulez restaurer la base de données.
3. Dans la boîte de dialogue **Nom de la base de données restaurée**, indiquez le nom de la base de données cible.
4. Le cas échéant, sélectionnez **Remplacer si une base de données du même nom existe déjà sur l’instance SQL sélectionnée**.
5. Sélectionnez **OK**.

    ![Indiquer des valeurs pour le menu de Configuration de la restauration](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Dans **point de restauration**, sélectionnez s’il faut [restaurer à un point spécifique dans le temps](#restore-to-a-specific-point-in-time) ou [restaurer à un point de récupération spécifique](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > La restauration de point-à-temps est disponible uniquement pour les sauvegardes de journal pour les bases de données qui sont en mode de récupération full et bulk-logged.

### <a name="restore-and-overwrite"></a>Restaurer et remplacer

1. Dans le **restaurer la Configuration de** menu, sous **où effectuer la restauration**, sélectionnez **remplacer la base de données** > **OK**.

    ![Sélectionner Remplacer la base de données](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Dans **point de restauration**, sélectionnez **journaux (Point dans le temps)** à [restaurer à un point spécifique dans le temps](#restore-to-a-specific-point-in-time). Ou sélectionnez **complète et différentielle** pour restaurer à un [point de récupération spécifique](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > La restauration de point-à-temps est disponible uniquement pour les sauvegardes de journal pour les bases de données qui sont en mode de récupération full et bulk-logged.

### <a name="restore-to-a-specific-point-in-time"></a>Restaurer à un point spécifique dans le temps

Si vous avez sélectionné **Journaux d’activité (point dans le temps)** comme type de restauration, effectuez les actions suivantes :

1.  Sous **restaurer de Date/heure**, ouvrez le calendrier. Dans le calendrier, les dates qui ont des points de récupération sont affichés en gras, et la date actuelle est mise en surbrillance.
1. Sélectionnez une date qui a des points de récupération. Vous ne pouvez pas sélectionner des dates qui n’ont aucun point de récupération.

    ![Ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Une fois que vous avez sélectionné une date, le graphique chronologique affiche les points de récupération disponibles dans une plage continue.
1. Spécifiez une heure pour la récupération sur le graphique de chronologie, ou sélectionnez une heure. Sélectionnez ensuite **OK**.

    ![Sélectionnez une heure de restauration](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. Sur le **Advanced Configuration** activer le menu, si vous souhaitez conserver la base de données non opérationnelle après la restauration, **Restore with NORECOVERY**.
1. Si vous voulez changer l’emplacement de restauration sur le serveur de destination, entrez un nouveau chemin cible.
1. Sélectionnez **OK**.

    ![Menu Configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Dans le menu **Restaurer**, sélectionnez **Restaurer** pour démarrer le travail de restauration.
1. Suivre la progression de la restauration dans le **Notifications** zone, ou le suivi en sélectionnant **travaux de restauration** dans le menu de la base de données.

    ![Progression du travail de restauration](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Restaurer à un point de restauration spécifique

Si vous avez sélectionné **Complète et différentielle** comme type de restauration, effectuez les actions suivantes :

1. Sélectionnez un point de récupération dans la liste, puis cliquez sur **OK** pour terminer la procédure de sélection du point de restauration.

    ![Choisir un point de récupération complète](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. Sur le **Advanced Configuration** activer le menu, si vous souhaitez conserver la base de données non opérationnelle après la restauration, **Restore with NORECOVERY**.
1. Si vous voulez changer l’emplacement de restauration sur le serveur de destination, entrez un nouveau chemin cible.
1. Sélectionnez **OK**.

    ![Menu Configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. Dans le menu **Restaurer**, sélectionnez **Restaurer** pour démarrer le travail de restauration.
1. Suivre la progression de la restauration dans le **Notifications** zone, ou le suivi en sélectionnant **travaux de restauration** dans le menu de la base de données.

    ![Progression du travail de restauration](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Restaurer des bases de données avec un grand nombre de fichiers

Si la taille de la chaîne total des fichiers dans une base de données est supérieure à un [limite particulière](backup-sql-server-azure-troubleshoot.md#files-size-limit-beyond-which-restore-happens-to-default-path), sauvegarde Azure stocke la liste des fichiers de base de données dans un composant pit différents tels que vous ne serez pas en mesure de définir le chemin d’accès de restauration cible pendant la restauration opération. Les fichiers seront restaurés vers le chemin d’accès par défaut SQL à la place.

  ![Restaurer la base de données avec des fichiers volumineux](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>Étapes suivantes

[Gérer et surveiller](manage-monitor-sql-database-backup.md) les bases de données SQL Server qui sont sauvegardés par sauvegarde Azure.
