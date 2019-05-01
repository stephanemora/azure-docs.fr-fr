---
title: Forum aux questions sur la sauvegarde de bases de données SQL Server sur des machines virtuelles Azure avec Sauvegarde Azure
description: Trouvez des réponses aux questions courantes sur la sauvegarde des bases de données SQL Server sur des machines virtuelles Azure avec sauvegarde Azure.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: sachdevaswati
ms.openlocfilehash: 649e50634d901ab48f1cb36c39d7331401c0cc51
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700172"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Forum aux questions sur les bases de données SQL Server qui sont exécutent sur une sauvegarde de machine virtuelle Azure

Cet article répond aux questions courantes sur la sauvegarde de bases de données SQL Server qui s’exécutent sur des machines virtuelles (VM) Azure et qui utilisent le [sauvegarde Azure](backup-overview.md) service.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Puis-je utiliser sauvegarde Azure pour les VM IaaS, ainsi que SQL Server sur le même ordinateur ?
Oui, vous pouvez avoir sauvegarde de machine virtuelle et sauvegarde SQL sur la même machine virtuelle. Dans ce cas, nous déclenchons en interne copie seule sauvegarde complète sur la machine virtuelle ne pas tronquer les journaux.


## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>La solution de nouvelle tentative ou les sauvegardes de correction automatique ?

Dans certaines circonstances, le service Azure Backup déclenche des sauvegardes de réparation. Correction automatique peut se produire pour une des six conditions mentionnées ci-dessous :

  - Si la sauvegarde de journal ou différentielle échoue en raison d’une erreur de Validation de LSN, prochaine sauvegarde de journal ou différentielle est convertie à la place d’une sauvegarde complète.
  - Si aucune sauvegarde complète n’a eu lieu avant une sauvegarde différentielle ou le journal, ce journal ou une sauvegarde différentielle est convertie à la place d’une sauvegarde complète.
  - Si point-à-temps de la dernière sauvegarde complète est antérieure à 15 jours, la sauvegarde différentielle ou journal suivant est converti à la place d’une sauvegarde complète.
  - Tous les travaux de sauvegarde annulées en raison d’une mise à niveau de l’extension sont ré-déclenchées après la mise à niveau est terminée et l’extension est démarrée.
  - Si vous choisissez de remplacer la base de données pendant la restauration, la prochaine sauvegarde du journal/différentielle échoue et une sauvegarde complète est déclenchée à la place.
  - Dans les cas où une sauvegarde complète est nécessaire pour redéfinir les chaînes de journal en raison de modifier en mode de récupération de base de données, un intégral obtient déclenché automatiquement sur la prochaine planification.

Correction automatique car une fonctionnalité est activée pour tous les utilisateurs par défaut ; Toutefois dans le cas où vous choisissez d’annulations de celui-ci, puis effectuez la ci-dessous :

  * Sur l’instance de SQL Server, dans le *Backup\bin de charge de travail C:\Program Files\Azure* dossier, créez ou modifiez la **ExtensionSettingsOverrides.json** fichier.
  * Dans le **ExtensionSettingsOverrides.json**, affectez la valeur *{« EnableAutoHealer » : false}*.
  * Enregistrez vos modifications et fermez le fichier.
  * Sur l’instance de SQL Server, ouvrez **tâche gérer** , puis redémarrez le **AzureWLBackupCoordinatorSvc** service.  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>Puis-je contrôler que d’exécution de nombre de sauvegardes simultané sur le serveur SQL server ?

Oui. Vous pouvez limiter la fréquence à laquelle la stratégie de sauvegarde s’exécute pour minimiser l’impact sur une instance SQL Server. Pour modifier ce paramètre :
1. Sur l’instance de SQL Server, dans le *Backup\bin de charge de travail C:\Program Files\Azure* dossier, créez le *ExtensionSettingsOverrides.json* fichier.
2. Dans le *ExtensionSettingsOverrides.json* , modifiez le **DefaultBackupTasksThreshold** définissant une valeur inférieure (par exemple, 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. Enregistrez vos modifications et fermez le fichier.
4. Sur l’instance SQL Server, ouvrez le **Gestionnaire des tâches**. Redémarrez le service **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Bien que cette méthode permet de si l’application de sauvegarde consomme beaucoup de ressources, SQL Server [du gouverneur de ressources](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) est un moyen plus générique pour spécifier des limites sur la quantité d’UC, e/s physiques et mémoire capable de demandes d’application entrantes utiliser.

> [!NOTE]
> Dans l’expérience utilisateur vous pouvez toujours poursuivre et planifier des sauvegardes autant à un moment donné, toutefois, ils seront traités dans une fenêtre glissante de dire que 5, conformément à l’exemple ci-dessus.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Puis-je effectuer la sauvegarde complète d’un réplica secondaire ?
Conformément à des limitations de SQL, vous pouvez exécuter la copie uniquement la sauvegarde complète sur le réplica secondaire ; Toutefois, une sauvegarde complète n’est pas autorisée.

## <a name="can-i-protect-availability-groups-on-premises"></a>Puis-je protéger disponibilité groupes locaux ?
Non. Azure Backup protège les bases de données SQL Server s’exécutant dans Azure. Si un groupe de disponibilité (AG) est réparti entre les machines Azure et sur site, le groupe de disponibilité peut être protégé uniquement si le réplica principal est en cours d’exécution dans Azure. En outre, Azure Backup protège uniquement les nœuds qui s’exécutent dans la même région Azure que le coffre Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Puis-je protéger des groupes de disponibilité entre les régions ?
Le coffre Azure Backup Recovery Services peut détecter et protéger tous les nœuds qui se trouvent dans la même région que le coffre. Si votre groupe de disponibilité SQL Server Always On s’étend sur plusieurs régions Azure, configurer la sauvegarde de la région qui possède le nœud principal. La Sauvegarde Azure peut découvrir et protéger toutes les bases de données du groupe de disponibilité en fonction de votre préférence de sauvegarde. Lorsque votre préférence de sauvegarde n’est pas remplie, les sauvegardes échouent et vous obtenez l’alerte d’échec.

## <a name="do-successful-backup-jobs-create-alerts"></a>La réussite des travaux de sauvegarde génère-t-elle des alertes ?
Non. Les travaux de sauvegarde réussis ne génèrent pas d’alertes. Les alertes ne sont envoyées qu’en cas d’échec de la sauvegarde. Comportement détaillé pour les alertes de portail est documenté [ici](backup-azure-monitoring-built-in-monitor.md). Toutefois, au cas où vous vous intéressez ont alertes même pour les travaux réussis, vous pouvez utiliser [analyse à l’aide d’Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Puis-je voir les travaux de sauvegarde planifiées dans le menu travaux de sauvegarde ?
Le **travail de sauvegarde** menu affiche uniquement les ad-hoc travaux de sauvegarde. Pour la tâche planifiée, utilisez [analyse à l’aide d’Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Les bases de données futures sont-elles automatiquement ajoutées pour la sauvegarde ?
Oui, vous pouvez obtenir cette fonctionnalité avec [la protection automatique](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Si je supprime une base de données à partir d’une instance autoprotected, que se passera-t-il pour les sauvegardes ?
Si une base de données est supprimée d’une instance autoprotected, les sauvegardes de base de données sont toujours tentées. Cela implique que la base de données commence à apparaître comme étant défectueux sous **éléments de sauvegarde** et reste protégé.

La méthode correcte pour arrêter la protection de cette base de données consiste à faire **arrêter la sauvegarde** avec **supprimer des données** sur cette base de données.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Si l’opération de sauvegarde d’une base de données autoprotected arrêter quel sera son comportement ?
Si vous le faites **arrêter la sauvegarde avec conservation des données**, aucune sauvegarde futures n’aura lieu et les points de récupération existants demeureront intactes. La base de données seront toujours considéré comme protégée et être indiqué sous la **éléments de sauvegarde**.

Si vous le faites **arrêter la sauvegarde avec suppression des données**, aucune sauvegarde futures n’aura lieu et les points de récupération existants seront également supprimés. La base de données sera considérée comme non protégé et être indiqué sous l’instance dans la configuration de la sauvegarde. Toutefois, contrairement à d’autres protégé par des bases de données qui peuvent être sélectionnés manuellement ou qui peut obtenir autoprotected, cette base de données est grisé et ne peut pas être sélectionné. La seule façon de protéger de nouveau cette base de données consiste à désactiver la protection automatique sur l’instance. Vous pouvez maintenant sélectionner cette base de données et configurer la protection sur ce dernier ou réactiver la protection automatique sur l’instance.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Si je modifie le nom de la base de données une fois qu’il a été protégé, quel sera le comportement ?
Une base de données renommée est traitée comme une base de données. Par conséquent, le service traite cette situation comme si la base de données est introuvables et avec les sauvegardes d’échouer.

Vous pouvez sélectionner la base de données, ce qui a été renommé et configurer la protection sur ce dernier. Dans le cas où la protection automatique est activée sur l’instance, la base de données renommée sera automatiquement détecté et protégé.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Pourquoi ne puis-je pas voir une base de données ajoutée pour une instance d’autoprotected ?
Une base de données que vous avez [ajouter à une instance d’autoprotected](backup-sql-server-database-azure-vms.md#enable-auto-protection) n’apparaisse pas immédiatement sous éléments protégés. C’est parce que la découverte s’exécute généralement toutes les 8 heures. Toutefois, vous pouvez découvrir et protéger immédiatement les nouvelles bases de données si vous exécutez manuellement une détection en sélectionnant **récupérer des bases de données**, comme illustré dans l’image suivante.

  ![Découvrir manuellement une base de données nouvellement ajoutée](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [sauvegarder une base de données SQL Server](backup-azure-sql-database.md) qui s’exécute sur une machine virtuelle Azure.
