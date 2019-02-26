---
title: Forum aux questions sur la sauvegarde de bases de données SQL Server sur des machines virtuelles Azure avec Sauvegarde Azure
description: Fournit des réponses aux questions courantes sur la sauvegarde de bases de données SQL Server sur des machines virtuelles Azure avec Sauvegarde Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430923"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>Forum aux questions sur SQL Server s’exécutant sur la sauvegarde de machine virtuelle Azure

Cet article fournit des réponses à des questions courantes sur la sauvegarde de bases de données SQL Server s’exécutant sur des machines virtuelles Azure avec le service [Sauvegarde Azure](backup-overview.md).

> [!NOTE]
> Cette fonctionnalité est actuellement disponible en préversion publique.



## <a name="can-i-throttle-the-backup-speed"></a>Puis-je limiter la vitesse de la sauvegarde ?

Oui. Vous pouvez limiter la fréquence à laquelle la stratégie de sauvegarde s’exécute pour minimiser l’impact sur une instance SQL Server. Pour modifier ce paramètre :
1. Sur l’instance SQL Server, dans le dossier *C:\Program Files\Azure Workload Backup\bin*, créez le fichier **ExtensionSettingsOverrides.json**.
2. Dans le fichier **ExtensionSettingsOverrides.json**, remplacez la valeur du paramètre **DefaultBackupTasksThreshold** par une valeur inférieure (par exemple, 5) <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Enregistrez vos modifications. Fermer le fichier.
4. Sur l’instance SQL Server, ouvrez le **Gestionnaire des tâches**. Redémarrez le service **AzureWLBackupCoordinatorSvc**.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Puis-je effectuer la sauvegarde complète d’un réplica secondaire ?
 Non. Cette fonctionnalité n'est pas prise en charge.

## <a name="do-successful-backup-jobs-create-alerts"></a>La réussite des travaux de sauvegarde génère-t-elle des alertes ?

 Non. Les travaux de sauvegarde réussis ne génèrent pas d’alertes. Les alertes ne sont envoyées qu’en cas d’échec de la sauvegarde.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>Les travaux de sauvegarde planifiés sont-ils affichés dans le menu Travaux ?

 Non. Le menu **Travaux de sauvegarde** affiche les détails des travaux à la demande, mais pas ceux des travaux de sauvegarde planifiés. Si un travail de sauvegarde échoue, les détails concernant cet échec sont fournis dans l’alerte d’échec. Si vous souhaitez surveiller tous les travaux de sauvegarde ad hoc et planifiés, utilisez [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Les bases de données futures sont-elles automatiquement ajoutées pour la sauvegarde ?

 Non. Lorsque vous configurez la protection pour une instance SQL Server, si vous sélectionnez l’option au niveau du serveur, toutes les bases de données sont ajoutées. Toutefois, si vous ajoutez des bases de données à une instance SQL Server après avoir configuré la protection, vous devez ajouter manuellement les nouvelles bases de données pour les protéger. Les bases de données ne sont pas automatiquement incluses dans la protection configurée.

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>Comment faire pour redémarrer la protection après avoir modifié le type de récupération ?

Déclenchez une sauvegarde complète. Les sauvegardes de fichier journal commencent comme prévu.

## <a name="can-i-protect-availability-groups-on-premises"></a>Puis-je protéger des groupes de disponibilité en local ?

 Non. Sauvegarde Azure protège les serveurs SQL en cours d’exécution dans Azure. Si un groupe de disponibilité (AG) est réparti entre les machines Azure et sur site, l’AG peut être protégé uniquement si le réplica principal est en cours d’exécution dans Azure. En outre, Sauvegarde Azure protège uniquement les nœuds en cours d’exécution dans la même région Azure que le coffre Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Puis-je protéger des groupes de disponibilité sur plusieurs régions ?

Le coffre Recovery Services de Sauvegarde Azure peut détecter et protéger tous les nœuds qui se trouvent dans la même région que le coffre Recovery Services. Si vous avez un groupe de disponibilité AlwaysOn SQL s’étendant sur plusieurs régions Azure, vous devez configurer la sauvegarde à partir de la région où se trouve le nœud principal. Sauvegarde Azure pourra alors détecter et protéger toutes les bases de données du groupe de disponibilité selon la préférence de sauvegarde. Si la préférence de sauvegarde n’est pas satisfaite, les sauvegardes échouent et vous recevez l’alerte d’échec.

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>Puis-je exclure des bases de données avec la protection automatique activée ?

Non, la [protection automatique](backup-azure-sql-database.md#enable-auto-protection) s’applique à l’ensemble de l’instance. Vous ne pouvez pas protéger de manière sélective les bases de données d’une instance à l’aide de la protection automatique.

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>Puis-je avoir différentes stratégies dans une instance auto-protégée ?

Si vous avez déjà des bases de données protégées dans une instance, elles restent protégées par leurs stratégies respectives même après l’**activation** de l’option de [protection automatique](backup-azure-sql-database.md#enable-auto-protection). Toutefois, toutes les bases de données non protégées ainsi que celles que vous ajoutez par la suite ont une seule stratégie que vous définissez sous **Configurer la sauvegarde** après avoir sélectionné les bases de données. En réalité, contrairement aux autres bases de données protégées, vous ne pouvez même pas changer la stratégie d’une base de données sous une instance protégée automatiquement.
Le seul moyen de le faire est de désactiver momentanément la protection automatique sur l’instance et de changer la stratégie pour cette base de données. Vous pouvez ensuite réactiver la protection automatique sur cette instance.

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>Si je supprime une base de données de la protection automatique, les sauvegardes seront-elles arrêtées ?

Non, si une base de données est supprimée d’une instance protégée automatiquement, les sauvegardes de cette base de données sont toujours essayées. Cela implique que la base de données supprimée devient défectueuse sous **Éléments de sauvegarde** et reste traitée comme si elle était protégée.

La seule façon d’arrêter la protection de ces bases de données consiste à désactiver momentanément la [protection automatique](backup-azure-sql-database.md#enable-auto-protection) sur l’instance et de choisir l’option **Arrêter la sauvegarde** sous **Éléments de sauvegarde** pour la base de données concernée. Vous pouvez ensuite réactiver la protection automatique sur cette instance.

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>Pourquoi ne puis-je pas voir une base de données ajoutée pour une instance protégée automatiquement ?

Vous pouvez ne pas voir une base de données qui vient d’être ajoutée à une instance [protégée automatiquement](backup-azure-sql-database.md#enable-auto-protection) de manière instantanée sous des éléments protégés. C’est parce que la découverte s’exécute généralement toutes les 8 heures. Toutefois, l’utilisateur peut exécuter une découverte manuelle à l’aide de l’option **Récupérer les bases de données** pour découvrir et protéger les nouvelles bases de données immédiatement, comme illustré dans l’image ci-dessous :

  ![Voir la base de données qui vient d’être ajoutée](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Étapes suivantes

[Apprenez](backup-azure-sql-database.md) à sauvegarder une base de données SQL Server exécutée sur une machine virtuelle Azure.
