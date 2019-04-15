---
title: Restaurer une base de données SQL Azure à partir d’une sauvegarde | Microsoft Docs
description: Apprenez-en plus sur la limite de restauration dans le temps, qui vous permet de restaurer une base de données SQL Azure à un point antérieur dans le temps (jusqu’à 35 jours).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: ca54ae11390b388c3158bd220ee5c7829172a5c3
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620476"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées d’une base de données

Par défaut, les sauvegardes de bases de données SQL sont placées dans l'espace de stockage blob géographiquement redondant (RA-GRS). Les options suivantes sont disponibles pour la récupération des bases de données à l’aide des [sauvegardes de bases de données automatisées](sql-database-automated-backups.md) :

- Créer une base de données sur le même serveur SQL Database récupéré à un point spécifié dans le temps durant la période de rétention.
- Créer une base de données sur le même serveur SQL Database récupéré à l’heure de suppression pour une base de données supprimée.
- Créer une base de données sur un serveur SQL Database dans la même région récupérée au point des sauvegardes les plus récentes.
- Créer une base de données sur un serveur SQL Database dans n’importe quelle autre région récupérée au point des sauvegardes répliquées les plus récentes.

Si vous avez configuré la [conservation à long terme des sauvegardes](sql-database-long-term-retention.md), vous pouvez également créer une base de données à partir de toute sauvegarde de conservation à long terme (LTR), sur n’importe quel serveur SQL Database et dans n’importe quelle région.

> [!IMPORTANT]
> Vous ne pouvez pas remplacer une base de données existante lors de la restauration.

Lorsque vous utilisez le niveau de service Standard ou Premium, une base de données restaurée implique un coût de stockage supplémentaire dans les conditions suivantes :

- La restauration de P11-P15 vers S4-S12 ou P1-P6 si la taille maximale de la base de données est supérieure à 500 Go.
- La restauration de P1-P6 vers S4-S12 si la taille maximale de la base de données est supérieure à 250 Go.

Un coût supplémentaire s’applique, car la taille maximale de la base de données restaurée est supérieure à la quantité de stockage incluse pour la taille de calcul, et le stockage configuré au-delà de la quantité incluse est facturé en plus. Pour les détails de la tarification du stockage supplémentaire, consultez la page [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage incluse, ce coût supplémentaire peut être évité en réduisant la taille maximale de la base de données à la quantité incluse.

> [!NOTE]
> Les [sauvegardes de base de données automatiques](sql-database-automated-backups.md) sont utilisées lorsque vous créez une [copie de base de données](sql-database-copy.md).

## <a name="recovery-time"></a>Temps de récupération

Le temps de récupération pour restaurer une base de données à l’aide des sauvegardes automatisées de bases de données est affecté par plusieurs facteurs :

- la taille de la base de données ;
- la taille de calcul de la base de données ;
- le nombre de journaux d’activité de transactions impliqués ;
- la quantité d’activité devant être relue pour effectuer une récupération au point de restauration ;
- la bande passante du réseau, si la restauration s’effectue dans une autre région ;
- le nombre de demandes de restauration simultanées en cours de traitement dans la région cible.

Pour une base de données très volumineuse et/ou très active, la restauration peut prendre plusieurs heures. En cas de panne prolongée dans une région, il est possible qu’un grand nombre de demandes de géorestauration soient traitées par d’autres régions. S’il y a un grand nombre de demandes, le temps de récupération des bases de données de cette région peut s’en trouver augmenté. La plupart des restaurations de bases de données s’effectuent au moins de 12 heures.

Pour un abonnement unique, des limitations s’appliquent sur le nombre de requêtes de restauration simultanées (y compris de restauration dans le temps, de restauration géographique et de restauration à partir d’une sauvegarde de rétention à long terme) soumises et effectuées :

| | **Nombre maximum de requêtes simultanées traitées** | **Nombre maximum de requêtes simultanées soumises** |
| :--- | --: | --: |
|Base de données unique (par abonnement)|10|60|
|Pool élastique (par pool)|4|200|
||||

Il n'existe aucune fonctionnalité intégrée pour une restauration en bloc. Le script [Base de données SQL Azure : récupération de serveur complète](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) est un exemple d’une façon d’accomplir cette tâche.

> [!IMPORTANT]
> Pour effectuer une récupération à l’aide de sauvegardes automatisées, vous devez avoir un rôle de collaborateur SQL Server dans l’abonnement ou être le propriétaire de l’abonnement. Voir [Rôles intégrés pour les ressources Azure](../role-based-access-control/built-in-roles.md). Vous pouvez effectuer une récupération en utilisant le portail Azure, PowerShell ou l’API REST. Vous ne pouvez pas utiliser Transact-SQL.

## <a name="point-in-time-restore"></a>Limite de restauration dans le temps

Vous pouvez restaurer une base de données autonome, en pool ou d’instance à un moment antérieur en tant que nouvelle base de données sur le même serveur à l’aide du portail Azure, de [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) ou de [l’API REST](https://docs.microsoft.com/rest/api/sql/databases). Une base de données peut être restaurée sur n’importe quel niveau de service ou taille de capacité de calcul. Assurez-vous d’avoir suffisamment de ressources sur le serveur vers lequel vous restaurez la base de données. Une fois le processus terminé, la base de données restaurée est une base de données normale entièrement accessible en ligne. La base de données restaurée est facturée aux tarifs habituels en fonction du niveau de service et de la taille de calcul. Aucun frais ne vous sera facturé jusqu’à ce que la restauration de la base de données soit terminée.

En règle générale, vous restaurez une base de données à un point antérieur à des fins de récupération. Dans ce cas, vous pouvez traiter la base de données restaurée comme remplacement de la base de données d’origine, ou l’utiliser pour en extraire des données afin de mettre à jour la base de données d’origine.

- **Remplacement de la base de données**

  Si la base de données restaurée est destinée à remplacer la base de données d’origine, vous devez vérifier que la taille de calcul et/ou le niveau de service sont appropriés, et effectuer une mise à l’échelle si nécessaire. Vous pouvez renommer la base de données d’origine, puis donner le nom d’origine à la base de données restaurée à l’aide de la commande [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) dans T-SQL.

- **Récupération des données**

  Si vous souhaitez récupérer des données à partir de la base de données restaurée suite à une erreur due à l'utilisateur ou à l'application, vous devez rédiger et exécuter les scripts de récupération de données nécessaires à l'extraction des données à partir de la base de données restaurée et les transférer vers la base de données d'origine. Bien que l’opération de restauration puisse prendre un certain temps, la base de données en cours de restauration sera visible dans la liste de bases de données pendant tout le processus de restauration. Si vous supprimez la base de données pendant la restauration, l’opération de restauration est annulée et vous ne serez pas facturé pour la base de données dont la restauration ne s’est pas terminée.

Pour récupérer une base de données unique, en pool ou d'instance à un moment spécifique à l’aide du portail Azure, ouvrez la page de votre base de données, puis cliquez sur **Restaurer** dans la barre d’outils.

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Pour restaurer par programmation une base de données à partir d’une sauvegarde, consultez [Exécution par programme d’une récupération à l’aide des sauvegardes automatisées](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups).

## <a name="deleted-database-restore"></a>Restauration d’une base de données supprimée

Vous pouvez restaurer une base de données supprimée à l’heure de sa suppression sur le même serveur SQL Database à l’aide du portail Azure, de [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) ou de [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Vous pouvez [restaurer une base de données supprimée sur Managed Instance à l’aide de PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). Vous pouvez restaurer une base de données supprimée à un point antérieur dans le temps au cours de la rétention à l’aide de [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase).

> [!TIP]
> Pour obtenir un exemple de script PowerShell montrant comment restaurer une base de données supprimée, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Si vous supprimez une instance de serveur Azure SQL Database, toutes ses bases de données sont également supprimées et ne peuvent pas être récupérées. Il n'existe aucune prise en charge pour la restauration d'un serveur supprimé pour l'instant.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Restauration d’une base de données supprimée à l’aide du portail Azure

Pour récupérer une base de données supprimée à l’aide du portail Azure pendant sa [période de rétention basée sur des DTU](sql-database-service-tiers-dtu.md) ou [période de rétention basée sur des vCores](sql-database-service-tiers-vcore.md), ouvrez la page de votre serveur et, dans la zone des opérations, cliquez sur **Bases de données supprimées**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Pour restaurer par programmation une base de données supprimée, consultez [Exécution par programme d’une récupération à l’aide des sauvegardes automatisées](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups).

## <a name="geo-restore"></a>Géo-restauration

Vous pouvez restaurer une base de données SQL sur n’importe quel serveur dans n’importe quelle région Azure à partir de la dernière sauvegarde géo-répliquée. La géorestauration utilise une sauvegarde géoredondante en tant que source et peut être mise à profit pour récupérer une base de données même si la base de données ou le centre de données est inaccessible en raison d’une défaillance.

La géorestauration constitue l’option de récupération par défaut lorsque votre base de données est indisponible en raison d’un incident dans la région où elle est hébergée. Si un incident à grande échelle dans une région entraîne l’indisponibilité de votre application de base de données, vous pouvez restaurer une base de données à partir des sauvegardes géorépliquées sur un serveur situé dans n’importe quelle autre région. Il peut y avoir un délai entre le moment où la sauvegarde est effectuée et celui où elle est géo-répliquée dans un objet blob Azure dans une autre région. Ce délai peut atteindre une heure. En cas d’incident, il peut donc y avoir jusqu’à une heure de pertes de données. L’illustration ci-dessous illustre la restauration de la base de données à partir de la dernière sauvegarde disponible dans une autre région.

![Restauration géographique](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Pour obtenir un exemple de script PowerShell montrant comment effectuer une géorestauration, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).

La restauration dans le temps sur un géo-réplica secondaire n’est pas prise en charge actuellement. La restauration dans le temps peut être effectuée uniquement sur une base de données primaire. Pour plus d’informations sur l’utilisation de la géorestauration pour la récupération suite à une panne, voir [Récupération après une panne](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> La récupération à partir des sauvegardes est la solution de récupération d’urgence la plus élémentaire proposée dans SQL Database, avec le RPO (objectif de point de récupération) et l’ERT (temps de récupération estimé) les plus longs. Pour les solutions utilisant des bases de données de petite taille (par exemple, des bases de données de niveau de service De base ou des bases de données de locataire de petite taille dans des pools élastiques), la géorestauration, qui présente un ERT de 12 heures maximum, constitue dans la plupart des cas une solution de récupération d’urgence convenable. Pour les solutions s’appuyant sur des bases de données de plus grande taille et nécessitant des temps de récupération plus courts, il est recommandé d’utiliser une [géoréplication active](sql-database-active-geo-replication.md) ou des [groupes de basculement automatique](sql-database-auto-failover-group.md). La géoréplication active offre un objectif de point de récupération et un temps de récupération estimé sensiblement inférieurs, car elle nécessite simplement un basculement vers une base de données secondaire répliquée en continu. Les groupes de basculement automatique permettent un basculement automatique pour un groupe de bases de données. Pour plus d’informations sur les choix de continuité d’activité, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>Géo-restauration à l’aide du portail Azure

Pour effectuer une géorestauration d’une base de données lors de sa [période de rétention basée sur des DTU](sql-database-service-tiers-dtu.md) ou [période de rétention basée sur des vCores](sql-database-service-tiers-vcore.md) à l’aide du portail Azure, ouvrez la page des bases de données SQL, puis cliquez sur **Ajouter**. Dans la zone de texte **Sélectionner une source**, sélectionnez **Sauvegarde**. Spécifiez la sauvegarde à partir de laquelle effectuer la récupération dans la région et sur le serveur de votre choix.

> [!Note]
> Géo-restauration à l’aide du portail Azure n’est pas disponible dans Managed Instance. Utilisez PowerShell.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Exécution par programme d’une récupération à l’aide des sauvegardes automatisées

Comme indiqué précédemment, en plus du Portail Azure, la récupération de la base de données peut être effectuée par programme à l’aide d’Azure PowerShell ou de l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

- Pour restaurer un travail autonome ou base de données regroupée, consultez [AzSqlDatabase de restauration](/powershell/module/az.sql/restore-azsqldatabase).

  | Applet de commande | Description |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtient une ou plusieurs bases de données. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtient une base de données supprimée que vous pouvez restaurer. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtient une sauvegarde géo-redondante d’une base de données. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaure une base de données SQL. |

  > [!TIP]
  > Pour obtenir un exemple de script PowerShell montrant comment restaurer une base de données à un point antérieur dans le temps, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).

- Pour restaurer une base de données de Managed Instance, consultez [AzSqlInstanceDatabase de restauration](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Applet de commande | Description |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Obtient un ou plusieurs instances gérées. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Obtient une instance de bases de données. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restaure une base de données d’instance. |

### <a name="rest-api"></a>API REST

Pour restaurer une base de données unique ou en pool à l’aide de l’API REST :

| API | Description |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Restaure une base de données |
| [Créer ou mettre à jour l’état de la base de données](https://docs.microsoft.com/rest/api/sql/operations) |Retourne l’état durant une opération de restauration |

### <a name="azure-cli"></a>Azure CLI

- Pour restaurer une base de données unique ou en pool à l’aide d’Azure CLI, consultez [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).
- Pour restaurer une instance gérée à l’aide d’Azure CLI, consultez [restauration de midb az sql](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Résumé

Les sauvegardes automatiques protègent vos bases de données des erreurs utilisateur et des erreurs d’application, de la suppression accidentelle d’une base de données et des interruptions prolongées. Cette fonctionnalité intégrée est disponible pour tous les niveaux de service et tailles de calcul.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
- Pour en savoir plus sur les sauvegardes automatisées Azure SQL Database, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
- Pour plus d’informations sur la rétention à long terme, consultez [Rétention à long terme](sql-database-long-term-retention.md).
- Pour découvrir des options de récupération plus rapides, consultez [Géoréplication active](sql-database-active-geo-replication.md) ou [Groupes de basculement automatique](sql-database-auto-failover-group.md).
