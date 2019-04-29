---
title: Gérer et surveiller des bases de données SQL Server sur une machine virtuelle Azure qui est sauvegardée par sauvegarde Azure | Microsoft Docs
description: Cet article décrit comment gérer et surveiller les bases de données SQL Server qui sont exécutent sur une machine virtuelle Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: ea5495867d5f453db014e000e01d533d049dc628
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849363"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gérer et surveiller des bases de données SQL Server sauvegardées

Cet article décrit les tâches courantes pour gérer et surveiller les bases de données SQL Server qui s’exécutent sur une machine virtuelle (VM) et qui sont sauvegardés pour une sauvegarde Azure Recovery Services vault par le [sauvegarde Azure](backup-overview.md) service. Vous allez apprendre à surveiller les travaux et alertes, arrêter et reprendre la protection de la base de données, exécuter des travaux de sauvegarde et annuler l’inscription d’une machine virtuelle à partir de sauvegardes.

Si vous n’avez pas encore configuré les sauvegardes pour vos bases de données SQL Server, consultez [sauvegarde des bases de données SQL Server sur des machines virtuelles Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Surveiller les travaux de sauvegarde manuelles dans le portail

Sauvegarde Azure montre tous les travaux déclenchées manuellement dans le **travaux de sauvegarde** portail. Les tâches de vous voir dans cette détection de base de données de portail include l’inscription et sauvegarde et les opérations de restauration.

![Le portail de travaux de sauvegarde](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Le **travaux de sauvegarde** portail n’affiche pas les travaux de sauvegarde planifiées. Utilisez SQL Server Management Studio pour surveiller les travaux de sauvegarde planifiés, comme décrit dans la section suivante.
>

Pour plus d’informations sur les scénarios d’analyse, accédez à [surveillance dans le portail Azure](backup-azure-monitoring-built-in-monitor.md) et [analyse à l’aide d’Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Afficher les alertes de sauvegarde

Comme les sauvegardes de fichiers journaux interviennent toutes les 15 minutes, la surveillance des tâches de sauvegarde peut s'avérer fastidieuse. Sauvegarde Azure facilite la surveillance en envoyant des alertes par courrier électronique. Alertes par courrier électronique sont :

- Déclenché pour tous les échecs de sauvegarde.
- Consolidées au niveau de la base de données par code d’erreur.
- Envoyées uniquement pour l’échec de la première sauvegarde de base de données.

Pour surveiller les alertes de sauvegarde de base de données :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sur le tableau de bord du coffre, sélectionnez **Alertes et événements**.

   ![Sélectionner Alertes et événements](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. Dans **Alertes et événements**, sélectionnez **Alertes de sauvegarde**.

   ![Sélectionner Alertes de sauvegarde](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Arrêter la protection d’une base de données SQL Server

Pour arrêter la sauvegarde d'une base de données SQL Server, deux méthodes sont disponibles :

* Arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération.
* Arrêtez toutes les futures tâches de sauvegarde et toucher les points de récupération.

Si vous choisissez de laisser des points de récupération, gardez à l’esprit ces détails :

* Tous les points de récupération sont conservés indéfiniment, nettoyage de tous les doit arrêter à arrêter la protection avec conservation des données.
* Vous êtes facturé pour l’instance protégée et le stockage consommé. Pour plus d’informations, consultez [tarification d’Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Si vous supprimez une source de données sans avoir à arrêter les sauvegardes, les nouvelles sauvegardes échouera.

Pour arrêter la protection de la base de données :

1. Dans le tableau de bord du coffre, sélectionnez **éléments de sauvegarde**.

2. Sous **Type de gestion de sauvegarde**, sélectionnez **SQL dans Azure VM**.

    ![Sélectionner SQL in Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Sélectionnez la base de données dont vous souhaitez arrêter la protection.

    ![Sélectionner la protection de la base de données a arrêter](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Dans le menu de la base de données, sélectionnez **Arrêter la sauvegarde**.

    ![Sélectionner Arrêter la sauvegarde](./media/backup-azure-sql-database/stop-db-button.png)


5. Sur le **arrêter la sauvegarde** menu, sélectionnez s’il faut conserver ou supprimer des données. Si vous le souhaitez, fournir une raison et un commentaire.

    ![Conserver ou supprimer des données dans le menu Arrêter la sauvegarde](./media/backup-azure-sql-database/stop-backup-button.png)

6. Sélectionnez **arrêter la sauvegarde**.


## <a name="resume-protection-for-a-sql-database"></a>Rétablir la protection d’une base de données SQL

Lorsque vous arrêtez la protection pour la base de données SQL, si vous sélectionnez le **conserver les données de sauvegarde** option, vous pouvez réactiver ultérieurement la protection. Si vous ne conservez pas les données de sauvegarde, vous ne pouvez pas reprendre la protection.

Pour reprendre la protection d’une base de données SQL :

1. Ouvrez l’élément de sauvegarde et sélectionnez **reprendre la sauvegarde**.

    ![Sélectionner Reprendre la sauvegarde pour relancer la protection de la base de données](./media/backup-azure-sql-database/resume-backup-button.png)

2. Dans le menu **Stratégie de sauvegarde**, sélectionnez une stratégie, puis sélectionnez **Enregistrer**.

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Vous pouvez exécuter différents types de sauvegardes à la demande :

* Sauvegarde complète
* Sauvegarde de copie uniquement
* Sauvegarde différentielle
* Sauvegarde du journal

Bien que vous devez spécifier la durée de rétention de sauvegarde complète de copie uniquement, la durée de rétention pour les autres types de sauvegarde est automatiquement définie sur 30 jours à partir de l’heure actuelle. <br/>
Pour plus d’informations, consultez [les types de sauvegarde de SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Supprimer l'inscription d'une instance SQL Server

Annuler l’inscription d’une instance de SQL Server une fois que vous désactivez la protection, mais avant de supprimer le coffre :

1. Le tableau de bord coffre sous **gérer**, sélectionnez **Infrastructure de sauvegarde**.  

   ![Choisir Infrastructure de sauvegarde](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Sous **Management Servers** (Serveurs d’administration), sélectionnez **Protected Servers** (serveurs protégés).

   ![Sélectionner Serveurs protégés](./media/backup-azure-sql-database/protected-servers.png)

3. Dans **Serveurs protégés**, sélectionnez le serveur dont vous souhaitez annuler l'inscription. Si vous souhaitez supprimer le coffre, vous devez annuler l’inscription de tous les serveurs.

4. Cliquez sur le serveur protégé, puis sélectionnez **supprimer**.

   ![Sélectionner Supprimer](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Extension de Re-register sur la machine virtuelle SQL Server

Parfois, l’extension de la charge de travail sur la machine virtuelle peut être affectée pour une raison ou l’autre. Dans ce cas, toutes les opérations déclenchées sur la machine virtuelle commencent à échouer. Vous devrez réinscrire l’extension sur la machine virtuelle. **Re-register** opération réinstalle l’extension de sauvegarde de la charge de travail sur la machine virtuelle pour les opérations de continuer.  <br>

Il est conseillé d’utiliser cette option avec précaution ; déclenchement de sur une machine virtuelle avec une extension intègre déjà, cette opération entraîne l’extension obtenir redémarré. Cela peut entraîner dans tous les travaux en cours d’exécution échoue. Veuillez consulter pour un ou plusieurs de la [symptômes](backup-sql-server-azure-troubleshoot.md#symptoms) avant de déclencher l’opération enregistrer à nouveau.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [résoudre les problèmes de sauvegardes sur une base de données SQL Server](backup-sql-server-azure-troubleshoot.md).
