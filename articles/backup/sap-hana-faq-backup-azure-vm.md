---
title: FAQ - Sauvegarder des bases de données SAP HANA sur des machines virtuelles Azure
description: Dans cet article, découvrez des réponses à des questions courantes sur la sauvegarde de bases de données SAP HANA avec le service Sauvegarde Microsoft Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155390"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Forum aux questions – Sauvegarde de bases de données SAP HANA sur des machines virtuelles Azure

Cet article fournit des réponses à des questions courantes sur la sauvegarde de bases de données SAP HANA avec le service Sauvegarde Microsoft Azure.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Combien de sauvegardes complètes sont-elles prises en charge par jour ?

Nous prenons en charge une seule sauvegarde complète par jour. Les sauvegardes différentielles et complètes ne peuvent pas être déclenchées le même jour.

### <a name="do-successful-backup-jobs-create-alerts"></a>La réussite des travaux de sauvegarde génère-t-elle des alertes ?

Non. Les travaux de sauvegarde réussis ne génèrent pas d’alertes. Les alertes ne sont envoyées qu’en cas d’échec de la sauvegarde. Le comportement détaillé des alertes de portail est détaillé [ici](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Toutefois, si vous souhaitez obtenir des alertes même pour des travaux réussis, vous pouvez utiliser [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Les travaux de sauvegarde planifiés sont-ils affichés dans le menu Travaux de sauvegarde ?

Le menu Travail de sauvegarde affiche uniquement les travaux de sauvegarde ad hoc. Pour les tâches planifiées, utilisez [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Les bases de données futures sont-elles automatiquement ajoutées pour la sauvegarde ?

Non, cela n’est actuellement pas pris en charge.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Si je supprime une base de données d’une instance, qu’advient-il des sauvegardes ?

Si une base de données est supprimée d’une instance SAP HANA, des tentatives de sauvegarde de cette base de données sont toujours effectuées. Cela implique que la base de données supprimée devient défectueuse sous **Éléments de sauvegarde** mais reste protégée.
Pour ne plus protéger cette base de données, utilisez **Arrêter la sauvegarde avec Supprimer des données** sur cette base de données.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Si je modifie le nom de la base de données après qu’elle a été protégée, comment se comportera-t-elle ?

Une base de données renommée est traitée en tant que nouvelle base de données. Par conséquent, le service gère cette situation comme si la base de données était introuvable et les sauvegardes échouent. La base de données renommée s’affiche sous la forme d’une nouvelle base de données et doit être configurée pour la protection.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quelles sont les conditions préalables à la sauvegarde des bases de données SAP HANA sur une machine virtuelle Azure ?

Reportez-vous aux sections [Prérequis](tutorial-backup-sap-hana-db.md#prerequisites) et [Ce que fait le script de préinscription](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Quelles autorisations doivent être définies pour qu’Azure puisse sauvegarder les bases de données SAP HANA ?

L’exécution du script de pré-inscription définit les autorisations requises pour permettre à Azure de sauvegarder des bases de données SAP HANA. Pour plus d’informations sur ce qu’un script de préinscription permet de faire, [cliquez ici](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Les sauvegardes seront-elles opérationnelles après la migration de SAP HANA de la version 1.0 à la version 2.0 ?

Reportez-vous à [cette section](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) du Guide de résolution des problèmes.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>La sauvegarde Azure HANA peut-elle être configurée sur une adresse IP virtuelle (équilibreur de charge) et non une machine virtuelle ?

Nous ne sommes pas en mesure de configurer la solution sur une adresse IP virtuelle seule pour le moment. Une machine virtuelle est nécessaire pour exécuter la solution.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>J’ai une réplication du système SAP HANA (HSR). Comment dois-je configurer la sauvegarde pour cette installation ?

Les nœuds principal et secondaire du HSR seront traités comme deux machines virtuelles indépendantes. Vous devez configurer la sauvegarde sur le nœud principal et, lorsque le basculement intervient, vous devez configurer la sauvegarde sur le nœud secondaire (qui devient alors le nœud principal). Il n’y a pas de basculement automatique de la sauvegarde vers l’autre nœud.

## <a name="restore"></a>Restaurer

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Pourquoi ne puis-je pas voir le système HANA sur lequel ma base de données doit être restaurée ?

Vérifiez que toutes les conditions préalables pour la restauration vers la cible de l’instance SAP HANA sont remplies. Pour plus d’informations, consultez [Conditions préalables - Restaurer des bases de données SAP HANA dans une machine virtuelle Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Pourquoi la restauration du remplacement de la base de données a-t-elle échoué pour ma base de données ?

Assurez-vous que l’option **Forcer le remplacement** est sélectionnée lors de la restauration.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Pourquoi l’erreur : « Les systèmes source et cible pour la restauration ne sont pas compatibles » apparaît-elle ?

Reportez-vous à la note SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) pour voir quels types de restauration sont actuellement pris en charge.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [sauvegarder des bases de données SAP HANA](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) qui s’exécutent sur des machines virtuelles Azure.
