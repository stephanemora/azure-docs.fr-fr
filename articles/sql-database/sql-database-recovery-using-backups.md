---
title: Restaurer une base de données Azure SQL à partir d’une sauvegarde | Microsoft Docs
description: Apprenez-en plus sur la limite de restauration dans le temps, qui vous permet de restaurer une base de données Azure SQL à un point antérieur dans le temps (jusqu’à 35 jours).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: 6f1a0485dbae3234d476ba5df62126f05d52f435
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813343"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Récupérer une base de données Azure SQL à l’aide des sauvegardes automatisées d’une base de données

Par défaut, les sauvegardes de bases de données SQL sont placées dans l'espace de stockage blob géographiquement redondant (RA-GRS). Les options suivantes sont disponibles pour la récupération des bases de données à l’aide des [sauvegardes de bases de données automatisées](sql-database-automated-backups.md) :

- Créer une base de données sur le même serveur SQL Database récupéré à un point spécifié dans le temps durant la période de rétention.
- Créer une base de données sur le même serveur SQL Database récupéré à l’heure de suppression pour une base de données supprimée.
- Créer une base de données sur un serveur SQL Database dans la même région récupérée au point des sauvegardes les plus récentes.
- Créer une base de données sur un serveur SQL Database dans n’importe quelle autre région récupérée au point des sauvegardes répliquées les plus récentes.

Si vous avez configuré la [conservation à long terme (LTR) des sauvegardes](sql-database-long-term-retention.md), vous pouvez également créer une base de données à partir d’une de ces sauvegardes LTR, sur n’importe quel serveur SQL Database.

> [!IMPORTANT]
> Vous ne pouvez pas remplacer une base de données existante lors de la restauration.

Lorsque vous utilisez le niveau de service Standard ou Premium, une base de données restaurée implique un coût de stockage supplémentaire dans les conditions suivantes :

- La restauration de P11-P15 vers S4-S12 ou P1-P6 si la taille maximale de la base de données est supérieure à 500 Go.
- La restauration de P1-P6 vers S4-S12 si la taille maximale de la base de données est supérieure à 250 Go.

Le coût supplémentaire s’applique si la taille maximale de la base de données restaurée est supérieure à la quantité de stockage incluse dans le niveau de service et de performance de la base de données cible. L’espace de stockage configuré au-delà de cette quantité est facturé en supplément. Pour les détails de la tarification du stockage supplémentaire, consultez la page [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage incluse, ce coût supplémentaire peut être évité en fixant la taille maximale de la base de données sur la quantité incluse.

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

Pour une base de données volumineuse ou très active, la restauration peut prendre plusieurs heures. En cas de panne prolongée dans une région, il est possible qu’un grand nombre de demandes de géorestauration soient traitées par d’autres régions. S’il y a un grand nombre de demandes, le temps de récupération des bases de données de cette région peut s’en trouver augmenté. La plupart des restaurations de bases de données s’effectuent au moins de 12 heures.

Pour un seul abonnement, le nombre de demandes de restauration simultanées est limité.  Ces plafonds s’appliquent à toutes les combinaisons possibles de limites de restauration dans le temps, de géorestaurations et de restaurations issues d’une sauvegarde de rétention à long terme) :

| | **Nombre maximum de requêtes simultanées traitées** | **Nombre maximum de requêtes simultanées soumises** |
| :--- | --: | --: |
|Base de données unique (par abonnement)|10|60|
|Pool élastique (par pool)|4|200|
||||

Il n’existe à l’heure actuelle aucune méthode intégrée permettant de restaurer l’intégralité du serveur. Le script [Azure SQL Database : récupération de serveur complète](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) illustre un moyen d’accomplir cette tâche.

> [!IMPORTANT]
> Pour effectuer une récupération à l’aide de sauvegardes automatisées, vous devez avoir un rôle de collaborateur SQL Server dans l’abonnement ou être le propriétaire de l’abonnement. Voir [Rôles intégrés pour les ressources Azure](../role-based-access-control/built-in-roles.md). Vous pouvez effectuer une récupération en utilisant le portail Azure, PowerShell ou l’API REST. Vous ne pouvez pas utiliser Transact-SQL.

## <a name="point-in-time-restore"></a>Limite de restauration dans le temps

Pour restaurer une base de données autonome, mise en pool ou d’instance à un moment antérieur, il est possible d’utiliser le Portail Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) ou [l’API REST](https://docs.microsoft.com/rest/api/sql/databases). La demande peut spécifier n’importe quel niveau de service ou taille de calcul pour la base de données restaurée. Assurez-vous d’avoir suffisamment de ressources sur le serveur vers lequel vous restaurez la base de données. À l’issue de l’opération, une base de données est créée sur le même serveur que la base de données d’origine. La base de données restaurée est facturée aux tarifs habituels en fonction du niveau de service et de la taille de calcul. Aucun frais ne vous sera facturé jusqu’à ce que la restauration de la base de données soit terminée.

En règle générale, vous restaurez une base de données à un point antérieur à des fins de récupération. Il est possible d’utiliser la base de données restaurée pour remplacer la base de données d’origine ou comme données sources afin de mettre à jour la base de données d’origine.

- **Remplacement de la base de données**

  Si la base de données restaurée est destinée à remplacer la base de données d’origine, il est recommandé de spécifier la taille de calcul et le niveau de service de la base de données d’origine. Vous pouvez alors renommer la base de données d’origine et donner le nom d’origine à la base de données restaurée avec la commande [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) dans T-SQL.

- **Récupération des données**

  Si vous souhaitez récupérer des données de la base de données restaurée suite à une erreur due à l'utilisateur ou à l'application, écrivez et exécutez un script de récupération de données qui extrait des données de la base de données restaurée et les applique à la base de données d'origine. Bien que l’opération de restauration puisse prendre un certain temps, la base de données en cours de restauration sera visible dans la liste de bases de données pendant tout le processus de restauration. Si vous supprimez la base de données pendant la restauration, l’opération de restauration est annulée et la base de données concernée ne vous est pas facturée.

Pour récupérer une base de données unique, mise en pool ou d'instance à un moment spécifique à l’aide du portail Azure, ouvrez la page de votre base de données, puis cliquez sur **Restaurer** dans la barre d’outils.

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Pour restaurer par programmation une base de données à partir d’une sauvegarde, consultez [Exécution par programme d’une récupération à l’aide des sauvegardes automatisées](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups).

## <a name="deleted-database-restore"></a>Restauration d’une base de données supprimée

Pour restaurer une base de données supprimée à l’heure de sa suppression ou avant sur le même serveur SQL Database, vous pouvez utiliser le Portail Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) ou [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Vous pouvez [restaurer une base de données supprimée sur Managed Instance à l’aide de PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Pour obtenir un exemple de script PowerShell montrant comment restaurer une base de données supprimée, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Si vous supprimez une instance de serveur Azure SQL Database, toutes ses bases de données sont également supprimées et ne peuvent pas être récupérées. Il n'existe aucune prise en charge pour la restauration d'un serveur supprimé pour l'instant.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Restauration d’une base de données supprimée à l’aide du portail Azure

Pour récupérer une base de données avec le Portail Azure, ouvrez la page de votre serveur et, dans la zone Opérations, cliquez sur **Bases de données supprimées**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Pour restaurer par programmation une base de données supprimée, consultez [Exécution par programme d’une récupération à l’aide des sauvegardes automatisées](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups).

## <a name="geo-restore"></a>Géo-restauration

Vous pouvez restaurer une base de données SQL sur n’importe quel serveur dans n’importe quelle région Azure à partir de la dernière sauvegarde géo-répliquée. La géorestauration utilise une sauvegarde géorépliquée comme source. Elle peut être demandée même si la base de données ou le centre de données est inaccessible en raison d’une panne.

La géorestauration constitue l’option de récupération par défaut lorsque la base de données n’est pas disponible en raison d’un incident dans la région d’hébergement. Vous pouvez restaurer la base de données sur un serveur dans n’importe quelle autre région. Il peut y avoir un délai entre le moment où la sauvegarde est effectuée et celui où elle est géo-répliquée dans un objet blob Azure dans une autre région. C’est pourquoi la base de données restaurée peut avoir jusqu’à une heure de retard par rapport à la base de données d’origine. L’illustration ci-dessous illustre la restauration de la base de données à partir de la dernière sauvegarde disponible dans une autre région.

![Restauration géographique](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Pour obtenir un exemple de script PowerShell montrant comment effectuer une géorestauration, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).

La restauration dans le temps sur un géo-réplica secondaire n’est pas prise en charge actuellement. La restauration dans le temps peut être effectuée uniquement sur une base de données primaire. Pour plus d’informations sur l’utilisation de la géorestauration pour la récupération suite à une panne, voir [Récupération après une panne](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> La géorestauration est la solution de reprise d’activité la plus basique de SQL Database. Elle s’appuie sur des sauvegardes géorépliquées créées automatiquement dont le RPO est égal à 1 h et la durée estimée de récupération à 12 heures maximum. Elle ne garantit pas que la région cible aura la capacité de restaurer la ou les bases de données après une panne régionale, car il se produira probablement un pic de demande. Il s’agit d’une solution de reprise d’activité adaptée aux applications non critiques pour l’entreprise qui utilisent des bases de données relativement petites. Pour les applications critiques qui utilisent de grosses bases de données et doivent assurer la continuité d’activité, utilisez plutôt des [Groupes de basculement automatique](sql-database-auto-failover-group.md). Ils offrent un RPO et un RTO beaucoup plus faibles, et la capacité est toujours garantie. Pour plus d’informations sur les choix de continuité d’activité, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>Géo-restauration à l’aide du portail Azure

Pour effectuer une géorestauration d’une base de données à l’aide du portail Azure, ouvrez la page SQL Database, puis cliquez sur **Ajouter**. Dans la zone de texte **Sélectionner une source**, sélectionnez **Sauvegarde**. Spécifiez la sauvegarde à partir de laquelle effectuer la récupération dans la région et sur le serveur de votre choix. 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Exécution par programme d’une récupération à l’aide des sauvegardes automatisées

Comme indiqué précédemment, en plus du Portail Azure, la récupération de la base de données peut être effectuée par programme à l’aide d’Azure PowerShell ou de l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements à venir sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes du module Az et des modules AzureRm sont sensiblement identiques.

- Pour restaurer une base de données autonome ou mise en pool, voir [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Applet de commande | Description |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtient une ou plusieurs bases de données. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtient une base de données supprimée que vous pouvez restaurer. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtient une sauvegarde géo-redondante d’une base de données. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaure une base de données SQL. |

  > [!TIP]
  > Pour obtenir un exemple de script PowerShell montrant comment restaurer une base de données à un point antérieur dans le temps, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).

- Pour restaurer une base de données Managed Instance, voir [AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Applet de commande | Description |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Récupère une ou plusieurs instances gérées. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Récupère une base de données d’instance. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restaure une base de données d’instance. |

### <a name="rest-api"></a>API REST

Pour restaurer une base de données unique ou mise en pool à l’aide de l’API REST :

| API | Description |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Restaure une base de données |
| [Créer ou mettre à jour l’état de la base de données](https://docs.microsoft.com/rest/api/sql/operations) |Retourne l’état durant une opération de restauration |

### <a name="azure-cli"></a>D’Azure CLI

- Pour restaurer une base de données unique ou mise en pool à l’aide d’Azure CLI, consultez [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).
- Pour restaurer une instance gérée avec Azure CLI, voir [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Résumé

Les sauvegardes automatiques protègent vos bases de données des erreurs utilisateur et des erreurs d’application, de la suppression accidentelle d’une base de données et des interruptions prolongées. Cette fonctionnalité intégrée est disponible pour tous les niveaux de service et tailles de calcul.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
- Pour en savoir plus sur les sauvegardes automatisées d’une base de données Azure SQL, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
- Pour plus d’informations sur la rétention à long terme, consultez [Rétention à long terme](sql-database-long-term-retention.md).
- Pour découvrir des options de récupération plus rapides, consultez [Géoréplication active](sql-database-active-geo-replication.md) ou [Groupes de basculement automatique](sql-database-auto-failover-group.md).
