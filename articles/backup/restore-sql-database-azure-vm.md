---
title: Restaurer des bases de données SQL Server sauvegardées sur une machine virtuelle Azure avec Sauvegarde Azure | Microsoft Docs
description: Cet article explique comment restaurer des bases de données SQL Server exécutées sur une machine virtuelle Azure et sauvegardées avec Sauvegarde Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 9b741f8562ae2e81d297357afd3b0e0e3976a248
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445438"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurer des bases de données SQL Server sur des machines virtuelles Azure 


Cet article décrit comment restaurer une base de données SQL Server exécutée sur une machine virtuelle Azure qui a été sauvegardée dans un coffre Azure Backup Recovery Services avec le service [Sauvegarde Azure](backup-overview.md).


> [!NOTE]
> La sauvegarde des bases de données SQL Server exécutées sur une machine virtuelle Azure avec le service Sauvegarde Azure est actuellement disponible en préversion publique.


Cet article explique comment restaurer des bases de données SQL Server. Si vous n’avez pas configuré la sauvegarde pour les bases de données, suivez les instructions fournies de [cet article](backup-azure-sql-database.md).



## <a name="about-restoring-databases"></a>À propos de la restauration de bases de données

Sauvegarde Azure peut restaurer des bases de données SQL Server s’exécutant sur des machines virtuelles Azure comme suit :

- Restaurer à une date ou une heure spécifique (à la seconde), en utilisant les sauvegardes des journaux des transactions. Sauvegarde Azure détermine automatiquement la sauvegarde différentielle complète appropriée, et la chaîne des sauvegardes de fichiers journaux nécessaires pour restaurer en fonction de la date/heure sélectionnée.
- Restaurer une sauvegarde complète ou différentielle spécifique à restaurer sur un point de récupération spécifique, au lieu d’une date/heure spécifique.


### <a name="prerequisites"></a>Prérequis

Avant de restaurer une base de données, notez les points suivants :

- Vous pouvez restaurer la base de données vers une instance SQL Server dans la même région Azure.
- Le serveur de destination doit être inscrit auprès du même coffre que la source.
- Pour restaurer une base de données chiffrée avec TDE sur un autre serveur SQL Server, vous devez d’abord [restaurer le certificat sur le serveur de destination](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Avant de déclencher une restauration de la base de données « master », démarrez l’instance SQL Server en mode mono-utilisateur, avec l’option de démarrage **-m AzureWorkloadBackup**.
    - La valeur de **-m** est le nom du client.
    - Seul le nom du client spécifié est autorisé à ouvrir la connexion.
- Pour toutes les bases de données système (model, master, msdb), arrêtez le service SQL Agent avant de déclencher la restauration.
- Fermez toutes les applications qui tentent de voler une connexion à l’une de ces bases de données.

## <a name="restore-a-database"></a>Restauration d’une base de données

Pour effectuer la restauration, vous avez besoin des autorisations suivantes :

* Les autorisations **Opérateur de sauvegarde** pour le coffre dans lequel vous effectuez la restauration.
* L’accès **Contributeur (écriture)** à la machine virtuelle source qui est sauvegardée.
* L’accès **Contributeur (écriture)** à la machine virtuelle cible :
    - Si vous restaurez sur la même machine virtuelle, ce sera la machine virtuelle source.
    - Si vous restaurez à un autre emplacement, ce sera la nouvelle machine virtuelle cible. 

Restaurez comme ceci :
1. Ouvrez le coffre dans lequel la machine virtuelle SQL Server est inscrite.
2. Dans le tableau de bord du coffre, sous **Utilisation**, sélectionnez **Éléments de sauvegarde**.

    ![Ouvrir le menu Éléments de sauvegarde](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Dans **Éléments de sauvegarde**, sous **Type de gestion des sauvegardes**, sélectionnez **SQL dans une machine virtuelle Azure**.

    ![Sélectionner SQL in Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Sélectionnez la base de données à restaurer.

    ![Sélectionner la base de données à restaurer](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Passez en revue le menu de la base de données. Il fournit des informations sur la sauvegarde de la base de données, notamment : 

    * Le point de restauration le plus ancien et le point de restauration le plus récent.
    * L’état de la sauvegarde du fichier journal pour les dernières 24 heures, pour les bases de données en mode de récupération Complet et utilisant les journaux de transactions, si elles sont configurées pour les sauvegardes du journal des transactions.

6. Cliquez sur **Restaurer la base de données**. 

    ![Sélectionner Restore DB](./media/backup-azure-sql-database/restore-db-button.png)

7. Dans **Configuration de la restauration**, spécifiez où vous voulez restaurer les données :
    - **Autre emplacement** : restaure la base de données vers un autre emplacement et conserve la base de données source d’origine.
    - **Remplacer la base de données** : restaure les données dans la même instance SQL Server que la source d’origine. Cette option permet de remplacer la base de données d’origine.

    > [!Important]
    > Si la base de données sélectionnée fait partie d’un groupe de disponibilité Always On, SQL Server n’autorise pas le remplacement de la base de données. Seul **Autre emplacement** est disponible.
    >

    ![Menu Configuration de la restauration](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Restaurer à un autre emplacement

1. Dans le menu **Configuration de la restauration**, sous **Où voulez-vous restaurer ?**, sélectionnez **Autre emplacement**.
2. Sélectionnez le nom du serveur et l’instance SQL Server où vous voulez restaurer la base de données.
3. Dans la zone **Nom de la base de données restaurée**, indiquez le nom de la base de données cible.
4. Le cas échéant, sélectionnez **Remplacer si une base de données du même nom existe déjà sur l’instance SQL sélectionnée**.
5. Cliquez sur **OK**.

    ![Indiquer des valeurs pour le menu de Configuration de la restauration](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Dans **Sélectionner un point de restauration**, choisissez de [restaurer à un point spécifique dans le temps](#restore-to-a-specific-point-in-time) ou de restaurer à un [point de récupération spécifique](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > La restauration à un point dans le temps est disponible seulement pour les sauvegardes de journaux pour les bases de données dont le mode de récupération est Complet et utilisant les journaux de transactions. 


### <a name="restore-and-overwrite"></a>Restaurer et remplacer

1. Dans le menu **Configuration de la restauration**, sous **Où voulez-vous restaurer ?**, sélectionnez **Remplacer la base de données** > **OK**.

    ![Sélectionner Remplacer la base de données](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Dans **Sélectionner un point de restauration**, sélectionnez **Journaux (point dans le temps) pour [restaurer à un point spécifique dans le temps](#restore-to-a-specific-point-in-time), ou **Complète et différentielle** pour restaurer à un [point de récupération spécifique](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > La restauration à un point dans le temps est disponible uniquement pour les sauvegardes de journaux des bases de données dont le mode de récupération est Complet et utilisant les journaux de transactions. 




    
### <a name="restore-to-a-specific-point-in-time"></a>Restaurer à un point spécifique dans le temps

Si vous avez sélectionné **Journaux (point dans le temps)** comme type de restauration, effectuez les actions suivantes :

1.  Sous **Date/heure de la restauration**, sélectionnez le mini-calendrier. Dans le **Calendrier**, les dates indiquées en gras ont des points de récupération, et la date du jour est mise en surbrillance.
2. Sélectionnez une date avec des points de récupération. Les dates sans points de récupération ne peuvent pas être sélectionnées.

    ![Ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

3. Une fois que vous avez sélectionné une date, le graphique chronologique affiche les points de récupération disponibles dans une plage continue.
4. Spécifiez une heure pour la récupération avec le graphique chronologique, ou sélectionnez une heure. Cliquez ensuite sur **OK**.

    ![Ouvrir le calendrier](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

 
4. Dans le menu **Configuration avancée**, si vous voulez conserver la base de données non opérationnelle après la restauration, activez **Restaurer avec l’option NORECOVERY**.
5. Si vous voulez changer l’emplacement de restauration sur le serveur de destination, entrez un nouveau chemin cible.
6. Cliquez sur **OK**.

    ![Menu Configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)


7. Dans le menu **Restaurer**, sélectionnez **Restaurer** pour démarrer le travail de restauration.
8. Suivez la progression de la restauration dans la zone **Notifications** ou sélectionnez **Travaux de restauration** dans le menu de la base de données.

    ![Progression du travail de restauration](./media/backup-azure-sql-database/restore-job-notification.png)



### <a name="restore-to-a-specific-restore-point"></a>Restaurer à un point de restauration spécifique

Si vous avez sélectionné **Complète et différentielle** comme type de restauration, effectuez les actions suivantes :


1. Sélectionnez un point de récupération dans la liste, puis cliquez sur **OK** pour effectuer la procédure de restauration.

    ![Choisir un point de récupération complète](./media/backup-azure-sql-database/choose-fd-recovery-point.png)
        
2. Dans le menu **Configuration avancée**, si vous voulez conserver la base de données non opérationnelle après la restauration, activez **Restaurer avec l’option NORECOVERY**.
3. Si vous voulez changer l’emplacement de restauration sur le serveur de destination, entrez un nouveau chemin cible. 
4. Cliquez sur **OK**.

    ![Menu Configuration avancée](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

7. Dans le menu **Restaurer**, sélectionnez **Restaurer** pour démarrer le travail de restauration.
8. Suivez la progression de la restauration dans la zone **Notifications** ou sélectionnez **Travaux de restauration** dans le menu de la base de données.

    ![Progression du travail de restauration](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>Étapes suivantes

[Gérer et surveiller](manage-monitor-sql-database-backup.md) des bases de données SQL Server sauvegardées par Sauvegarde Azure.
