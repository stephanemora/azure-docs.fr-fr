---
title: Forum aux questions sur la sauvegarde de bases de données SQL Server sur des machines virtuelles Azure avec Sauvegarde Azure
description: Trouvez des réponses aux questions courantes sur la sauvegarde des bases de données SQL Server sur des machines virtuelles Azure avec sauvegarde Azure.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sachdevaswati
ms.openlocfilehash: 3d02c8b3d763e3bd767964c8056fa948bfbb13d1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996049"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Forum aux questions sur les bases de données SQL Server qui sont exécutent sur une sauvegarde de machine virtuelle Azure

Cet article répond aux questions courantes sur la sauvegarde de bases de données SQL Server qui s’exécutent sur des machines virtuelles (VM) Azure et qui utilisent le [sauvegarde Azure](backup-overview.md) service.

> [!NOTE]
> Cette fonctionnalité est actuellement disponible en préversion publique.

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>Puis-je contrôler que d’exécution de nombre de sauvegardes simultané sur le serveur SQL server ?

Oui. Vous pouvez limiter la fréquence à laquelle la stratégie de sauvegarde s’exécute pour minimiser l’impact sur une instance SQL Server. Pour modifier ce paramètre :
1. Sur l’instance de SQL Server, dans le *Backup\bin de charge de travail C:\Program Files\Azure* dossier, créez le *ExtensionSettingsOverrides.json* fichier.
2. Dans le *ExtensionSettingsOverrides.json* , modifiez le **DefaultBackupTasksThreshold** définissant une valeur inférieure (par exemple, 5). <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Enregistrez vos modifications et fermez le fichier.
4. Sur l’instance SQL Server, ouvrez le **Gestionnaire des tâches**. Redémarrez le service **AzureWLBackupCoordinatorSvc**.

> [!NOTE]
> Dans l’expérience utilisateur vous pouvez toujours poursuivre et planifier des sauvegardes autant à un moment donné, toutefois, ils seront traités dans une fenêtre glissante de dire que 5, conformément à l’exemple ci-dessus.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Puis-je effectuer la sauvegarde complète d’un réplica secondaire ?
Conformément à des limitations de SQL, vous pouvez exécuter la copie uniquement la sauvegarde complète sur le réplica secondaire ; Toutefois, une sauvegarde complète n’est pas autorisée.

## <a name="do-successful-backup-jobs-create-alerts"></a>La réussite des travaux de sauvegarde génère-t-elle des alertes ?
Non. Les travaux de sauvegarde réussis ne génèrent pas d’alertes. Les alertes ne sont envoyées qu’en cas d’échec de la sauvegarde.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>Puis-je voir les travaux de sauvegarde planifiées dans le menu travaux ?
Le **travail de sauvegarde** menu affiche uniquement les ad-hoc travaux de sauvegarde. Pour la tâche planifiée, utilisez [analyse à l’aide d’Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Les bases de données futures sont-elles automatiquement ajoutées pour la sauvegarde ?
Oui, avec [la protection automatique](backup-azure-sql-database.md#enable-auto-protection)

## <a name="can-i-protect-availability-groups-on-premises"></a>Puis-je protéger disponibilité groupes locaux ?
Non. Azure Backup protège les bases de données SQL Server s’exécutant dans Azure. Si un groupe de disponibilité (AG) est réparti entre les machines Azure et sur site, le groupe de disponibilité peut être protégé uniquement si le réplica principal est en cours d’exécution dans Azure. En outre, Azure Backup protège uniquement les nœuds qui s’exécutent dans la même région Azure que le coffre Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Puis-je protéger des groupes de disponibilité entre les régions ?
Le coffre Azure Backup Recovery Services peut détecter et protéger tous les nœuds qui se trouvent dans la même région que le coffre. Si votre groupe de disponibilité SQL Server Always On s’étend sur plusieurs régions Azure, configurer la sauvegarde de la région qui possède le nœud principal. Sauvegarde Azure peut détecter et protéger toutes les bases de données dans le groupe de disponibilité en fonction de vos préférences de sauvegarde. Lorsque votre préférence de sauvegarde n’est pas remplie, les sauvegardes échouent et vous obtenez l’alerte d’échec.

## <a name="can-i-exclude-databases-with-autoprotection-enabled"></a>Puis-je exclure des bases de données avec autoprotection activée ?
Non. Autoprotection [s’applique à l’intégralité de l’instance](backup-azure-sql-database.md#enable-auto-protection). Vous ne pouvez pas utiliser autoprotection pour protéger les bases de données dans une instance de manière sélective.

## <a name="can-i-have-different-policies-in-an-autoprotected-instance"></a>Puis-je avoir des stratégies différentes dans une instance d’autoprotected ?
Si votre instance comprend déjà certaines bases de données protégées, ils continuent à être protégées par leurs politiques même après avoir [allumer autoprotection](backup-azure-sql-database.md#enable-auto-protection). Toutefois, toutes les bases de données non protégées et les bases de données que vous ajouterez ultérieurement aura qu’une seule stratégie. Vous définissez cette stratégie sous **configurer la sauvegarde** après avoir sélectionné les bases de données. En fait, contrairement à d’autres bases de données protégées, vous ne pouvez même modifier la stratégie pour une base de données qui se trouve dans une instance d’autoprotected.
La seule façon de modifier la stratégie de cette base de données consiste à désactiver temporairement autoprotection pour l’instance. Puis vous réactivez autoprotection pour l’instance.

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-will-backups-stop"></a>Si je supprime une base de données à partir d’une instance autoprotected, sauvegardes cessera de ?
Non. Si une base de données est supprimée d’une instance autoprotected, les sauvegardes de base de données sont toujours tentées. Cela implique que la base de données commence à apparaître comme étant défectueux sous **éléments de sauvegarde** et reste protégé.

La seule façon d’arrêter la protection de cette base de données est temporairement [désactiver autoprotection](backup-azure-sql-database.md#enable-auto-protection) sur l’instance. Ensuite, sous **éléments de sauvegarde** pour la base de données, sélectionnez **arrêter la sauvegarde**. Puis, vous réactivez autoprotection pour cette instance.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Pourquoi ne puis-je pas voir une base de données ajoutée pour une instance d’autoprotected ?
Une base de données que vous avez [ajouter à une instance d’autoprotected](backup-azure-sql-database.md#enable-auto-protection) n’apparaisse pas immédiatement sous éléments protégés. C’est parce que la découverte s’exécute généralement toutes les 8 heures. Toutefois, vous pouvez découvrir et protéger immédiatement les nouvelles bases de données si vous exécutez manuellement une détection en sélectionnant **récupérer des bases de données**, comme illustré dans l’image suivante.

  ![Découvrir manuellement une base de données nouvellement ajoutée](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [sauvegarder une base de données SQL Server](backup-azure-sql-database.md) qui s’exécute sur une machine virtuelle Azure.
