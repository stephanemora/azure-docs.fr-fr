---
title: Restaurer une base de données à partir d’un fichier de sauvegarde
description: Apprenez-en davantage sur la limite de restauration dans le temps, qui vous permet de restaurer une base de données Azure SQL jusqu’à 35 jours en arrière.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: 3b0b5b02fa8f369bdfa03726bd5649b70b7bbd48
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228044"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Récupérer une base de données Azure SQL à l’aide des sauvegardes de bases de données automatisées

Par défaut, les sauvegardes Azure SQL Database sont stockées dans un stockage blob géorépliqué (type de stockage RA-GRS). Les options suivantes sont disponibles pour la récupération des bases de données à l’aide des [sauvegardes de bases de données automatisées](sql-database-automated-backups.md) : Vous pouvez :

- Créer une base de données sur le même serveur SQL Database, récupéré à un point spécifié dans le temps durant la période de conservation.
- Créer une base de données sur le même serveur SQL Database, récupéré à l’heure de suppression s’il s’agit d’une base de données supprimée.
- Créer une base de données sur un serveur SQL Database dans la même région, récupérée au point des sauvegardes les plus récentes.
- Créer une base de données sur un serveur SQL Database dans n’importe quelle autre région, récupérée au point des sauvegardes répliquées les plus récentes.

Si vous avez configuré la [conservation à long terme (LTR) des sauvegardes](sql-database-long-term-retention.md), vous pouvez également créer une base de données à partir d’une de ces sauvegardes LTR, sur n’importe quel serveur SQL Database.

> [!IMPORTANT]
> Vous ne pouvez pas remplacer une base de données existante lors de la restauration.

Quand vous utilisez le niveau de service Standard ou Premium, la restauration de votre base de données peut entraîner un coût de stockage supplémentaire. Le coût supplémentaire s’applique si la taille maximale de la base de données restaurée est supérieure à la quantité de stockage incluse dans le niveau de service et de performance de la base de données cible. Pour les détails de la tarification du stockage supplémentaire, consultez la page [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage incluse, ce coût supplémentaire peut être évité en fixant la taille maximale de la base de données sur la quantité incluse.

## <a name="recovery-time"></a>Temps de récupération

Le temps de récupération pour restaurer une base de données à l’aide des sauvegardes de bases de données automatisées est affecté par plusieurs facteurs :

- La taille de la base de données
- La taille de calcul de la base de données
- Le nombre de journaux de transactions impliqués
- La quantité d’activité devant être relue pour effectuer une récupération au point de restauration
- La bande passante du réseau, si la restauration s’effectue dans une autre région
- le nombre de demandes de restauration simultanées en cours de traitement dans la région cible.

Pour une base de données volumineuse ou très active, la restauration peut prendre plusieurs heures. En cas de panne prolongée dans une région, il est possible qu’un grand nombre de requêtes de géo-restauration soient lancées pour la récupération d’urgence. S’il y a un grand nombre de requêtes, le temps de récupération des bases de données individuelles peut s’en trouver augmenté. La plupart des restaurations de bases de données s’effectuent au moins de 12 heures.

Pour un seul abonnement, le nombre de requêtes de restauration simultanées est limité. Ces limitations s’appliquent à toutes les combinaisons de limites de restauration dans le temps, de géorestaurations et de restaurations issues d’une sauvegarde de conservation à long terme.

| | **Nombre maximum de requêtes simultanées traitées** | **Nombre maximum de requêtes simultanées soumises** |
| :--- | --: | --: |
|Base de données unique (par abonnement)|10|60|
|Pool élastique (par pool)|4|200|
||||

Il n’existe aucune méthode intégrée permettant de restaurer l’intégralité du serveur. Pour obtenir un exemple d’exécution de cette tâche, consultez [Azure SQL Database : Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Pour effectuer une récupération à l’aide de sauvegardes automatisées, vous devez avoir un rôle de contributeur SQL Server dans l’abonnement ou être le propriétaire de l’abonnement. Pour plus d’informations, consultez [RBAC : pour les ressources Azure](../role-based-access-control/built-in-roles.md). Vous pouvez effectuer une récupération en utilisant le portail Azure, PowerShell ou l’API REST. Vous ne pouvez pas utiliser Transact-SQL.

## <a name="point-in-time-restore"></a>Limite de restauration dans le temps

Pour restaurer une base de données autonome, mise en pool ou d’instance à un moment antérieur, vous pouvez utiliser le portail Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) ou l’[API REST](https://docs.microsoft.com/rest/api/sql/databases). La demande peut spécifier n’importe quel niveau de service ou taille de calcul pour la base de données restaurée. Assurez-vous d’avoir suffisamment de ressources sur le serveur vers lequel vous restaurez la base de données. Une fois terminée, la restauration crée une base de données sur le même serveur que la base de données d’origine. La base de données restaurée est facturée aux tarifs habituels, en fonction du niveau de service et de la taille de calcul. Aucun frais ne vous sera facturé jusqu’à ce que la restauration de la base de données soit terminée.

En règle générale, vous restaurez une base de données à un point antérieur à des fins de récupération. Il est possible d’utiliser la base de données restaurée pour remplacer la base de données d’origine ou comme source de données afin de mettre à jour la base de données d’origine.

- **Remplacement de la base de données**

  Si la base de données restaurée est destinée à remplacer la base de données d’origine, vous devez spécifier la taille de calcul et le niveau de service de la base de données d’origine. Vous pouvez alors renommer la base de données d’origine et donner le nom d’origine à la base de données restaurée avec la commande [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) dans T-SQL.

- **Récupération des données**

  Si vous souhaitez récupérer des données de la base de données restaurée suite à une erreur due à l'utilisateur ou à l'application, écrivez et exécutez un script de récupération de données qui extrait des données de la base de données restaurée et les applique à la base de données d'origine. Bien que l’opération de restauration puisse prendre un certain temps, la base de données en cours de restauration sera visible dans la liste de bases de données pendant tout le processus de restauration. Si vous supprimez la base de données pendant la restauration, l’opération de restauration est annulée et la base de données concernée ne vous est pas facturée.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Limite de restauration dans le temps à l’aide du portail Azure

Vous pouvez récupérer une base de données SQL ou une base de données d’instance unique à un point dans le temps à partir du panneau de vue d’ensemble de la base de données que vous souhaitez restaurer dans le portail Azure.

#### <a name="single-azure-sql-database"></a>Base de données Azure SQL unique

Pour récupérer une base de données unique ou mise en pool à un point dans le temps à l’aide du portail Azure, ouvrez la page de vue d’ensemble de la base de données, puis sélectionnez **Restaurer** dans la barre d’outils. Choisissez une source de sauvegarde et sélectionnez le point de sauvegarde dans le temps à partir duquel une nouvelle base de données sera créée. 

  ![Capture d’écran des options de restauration de base de données](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Base de données d’instance managée

Pour récupérer une base de données d’instance managée à un point dans le temps à l’aide du portail Azure, ouvrez la page de vue d’ensemble de la base de données, puis sélectionnez **Restaurer** dans la barre d’outils. Choisissez le point de sauvegarde dans le temps à partir duquel une nouvelle base de données sera créée. 

  ![Capture d’écran des options de restauration de base de données](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Pour restaurer par programmation une base de données à partir d’une sauvegarde, consultez [Exécution par programmation d’une récupération à l’aide des sauvegardes automatisées](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Restauration d’une base de données supprimée

Vous pouvez restaurer une base de données supprimée à l’heure de sa suppression, ou à un point dans le temps antérieur, sur le même serveur SQL Database ou la même instance managée. Vous pouvez effectuer cette opération par le biais du portail Azure, de [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) ou de [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Vous restaurez une base de données supprimée en créant une nouvelle base de données à partir de la sauvegarde.

> [!IMPORTANT]
> Si vous supprimez un serveur ou une instance managée Azure SQL Database, toutes ses bases de données sont aussi supprimées et leur récupération est impossible. Vous ne pouvez pas restaurer un serveur ou une instance managée supprimé(e).

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Restauration d’une base de données supprimée à l’aide du portail Azure

Vous pouvez restaurer des bases de données supprimées à partir du portail Azure à partir de la ressource de serveur et d’instance.

#### <a name="single-azure-sql-database"></a>Base de données Azure SQL unique

Pour récupérer une base de données unique ou mise en pool qui a été supprimée telle qu’elle était au moment de sa suppression à partir du portail Azure, ouvrez la page de vue d’ensemble du serveur, puis sélectionnez **Bases de données supprimées**. Sélectionnez une base de données supprimée que vous souhaitez restaurer, puis tapez le nom de la nouvelle base de données qui sera créée avec les données restaurées à partir de la sauvegarde.

  ![Capture d’écran de la restauration d’une base de données Azure SQL supprimée](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Base de données d’instance managée

Pour récupérer une base de données managée à partir du portail Azure, ouvrez la page de vue d’ensemble de l’instance managée, puis sélectionnez **Bases de données supprimées**. Sélectionnez une base de données supprimée que vous souhaitez restaurer, puis tapez le nom de la nouvelle base de données qui sera créée avec les données restaurées à partir de la sauvegarde.

  ![Capture d’écran de la restauration d’une base de données d’instance Azure SQL supprimée](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Restauration d’une base de données supprimée à l’aide de PowerShell

Utilisez les exemples de scripts suivants pour restaurer une base de données supprimée pour Azure SQL Database et une instance managée à l’aide de PowerShell.

#### <a name="single-azure-sql-database"></a>Base de données Azure SQL unique

Pour un exemple de script PowerShell montrant comment restaurer une base de données Azure SQL supprimée, voir [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Base de données d’instance managée

Pour obtenir un exemple de script PowerShell montrant comment restaurer une base de données d’instance supprimée, consultez [Restore deleted database on managed instance using PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance) (Restaurer une base de données supprimée sur Managed Instance à l’aide de PowerShell). 

> [!TIP]
> Pour restaurer par programmation une base de données supprimée, consultez [Exécution par programmation d’une récupération à l’aide des sauvegardes automatisées](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Géo-restauration

Vous pouvez restaurer une base de données SQL sur n’importe quel serveur dans n’importe quelle région Azure à partir de la dernière sauvegarde géo-répliquée. La géorestauration utilise une sauvegarde géorépliquée comme source. Vous pouvez demander une géorestauration même si la base de données ou le centre de données est inaccessible en raison d’une panne.

La géorestauration constitue l’option de récupération par défaut lorsque la base de données n’est pas disponible en raison d’un incident dans la région d’hébergement. Vous pouvez restaurer la base de données sur un serveur dans n’importe quelle autre région. Il peut y avoir un délai entre le moment où la sauvegarde est effectuée et celui où elle est géo-répliquée dans un objet blob Azure dans une autre région. C’est pourquoi la base de données restaurée peut avoir jusqu’à une heure de retard par rapport à la base de données d’origine. L’illustration ci-dessous montre la restauration d’une base de données à partir de la dernière sauvegarde disponible dans une autre région.

![Graphique de géorestauration](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Géorestauration à l’aide du portail Azure

À partir du portail Azure, créez une base de données d’instance unique ou managée, puis sélectionnez une sauvegarde de géorestauration disponible. La nouvelle base de données contient les données de sauvegarde géorestaurées.

#### <a name="single-azure-sql-database"></a>Base de données Azure SQL unique

Pour géorestaurer une base de données SQL unique à partir du portail Azure dans la région et le serveur de votre choix, effectuez les étapes suivantes :

1. À partir du **Tableau de bord**, sélectionnez **Ajouter** > **Créer une base de données SQL**. Sous l’onglet **De base**, entrez les informations nécessaires.
2. Sélectionnez **Paramètres supplémentaires**.
3. Pour **Utiliser des données existantes**, sélectionnez **Sauvegarde**.
4. Pour **Sauvegarde**, sélectionnez une sauvegarde dans la liste des sauvegardes de géorestauration disponibles.

    ![Capture d’écran des options de création de base de données SQL](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Terminez le processus de création d’une nouvelle base de données depuis la sauvegarde. Quand vous créez la base de données Azure SQL unique, elle contient la sauvegarde de géorestauration restaurée.

#### <a name="managed-instance-database"></a>Base de données d’instance managée

Pour géorestaurer une base de données d’instance managée du portail Azure vers une instance managée existante dans une région de votre choix, sélectionnez une instance managée sur laquelle vous souhaitez restaurer une base de données. Procédez comme suit :

1. Sélectionnez **Nouvelle base de données**.
2. Tapez le nom de base de données souhaité.
3. Sous **Utiliser des données existantes**, sélectionnez **Sauvegarde**.
4. Sélectionnez une sauvegarde dans la liste des sauvegardes de géorestauration disponibles.

    ![Capture d’écran des options de nouvelle base de données](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Terminez le processus de création de base de données. Quand vous créez la base de données d’instance, elle contient la sauvegarde de géorestauration restaurée.

### <a name="geo-restore-by-using-powershell"></a>Géorestaurer à l’aide de PowerShell

#### <a name="single-azure-sql-database"></a>Base de données Azure SQL unique

Pour obtenir un script PowerShell qui illustre comment effectuer une géorestauration pour une base de données SQL unique, consultez [Utiliser PowerShell pour restaurer une base de données unique Azure SQL à un point antérieur dans le passé](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Base de données d’instance managée

Pour obtenir un script PowerShell qui illustre comment effectuer une géorestauration pour une base de données d’instance managée, consultez [Utiliser PowerShell pour restaurer une base de données Managed Instance dans une autre région de zone géographique](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Considérations en matière de géorestauration

Vous ne pouvez pas effectuer une limite de restauration dans le temps sur une base de données géosecondaire. Vous ne pouvez le faire que sur une base de données primaire. Pour plus d’informations sur l’utilisation de la géorestauration pour la récupération suite à une panne, voir [Récupération après une panne](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> La géorestauration est la solution de reprise d’activité la plus basique de SQL Database. Elle s’appuie sur des sauvegardes géorépliquées créées automatiquement dont l’objectif de point de récupération (RPO) est égal à 1 heure et la durée estimée de récupération à 12 heures maximum. Elle ne garantit pas que la région cible aura la capacité de restaurer les bases de données après une panne régionale, car il se produira probablement un pic de demande. Si votre application utilise des bases de données relativement petites et qu’elle n’est pas vitale pour l’entreprise, la géorestauration est une solution de reprise d’activité adaptée. Pour les applications vitales pour l’entreprise qui utilisent de grosses bases de données et doivent assurer la continuité de l’activité, utilisez des [Groupes de basculement automatique](sql-database-auto-failover-group.md). Ils offrent un RPO et un objectif de délai de récupération beaucoup plus faibles, et la capacité est toujours garantie. Pour plus d’informations sur les choix de continuité d’activité, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Exécution par programmation d’une récupération à l’aide des sauvegardes automatisées

Vous pouvez également utiliser Azure PowerShell ou l’API REST pour la récupération. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements à venir sont destinés au module Az.Sql. Pour ces cmdlets, voir [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont très similaires à ceux dans les modules AzureRm.

#### <a name="single-azure-sql-database"></a>Base de données Azure SQL unique

Pour restaurer une base de données autonome ou mise en pool, voir [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Applet de commande | Description |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtient une ou plusieurs bases de données. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtient une base de données supprimée que vous pouvez restaurer. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtient une sauvegarde géo-redondante d’une base de données. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaure une base de données SQL. |

  > [!TIP]
  > Pour obtenir un exemple de script PowerShell qui montre comment restaurer une base de données à un point antérieur dans le temps, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Base de données d’instance managée

Pour restaurer une base de données d’instance managée, consultez [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Applet de commande | Description |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Récupère une ou plusieurs instances gérées. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Récupère une base de données d’instance. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restaure une base de données d’instance. |

### <a name="rest-api"></a>API REST

Pour restaurer une base de données unique ou mise en pool à l’aide de l’API REST :

| API | Description |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Restaure une base de données. |
| [Créer ou mettre à jour l’état de la base de données](https://docs.microsoft.com/rest/api/sql/operations) |Retourne l’état durant une opération de restauration. |

### <a name="azure-cli"></a>D’Azure CLI

#### <a name="single-azure-sql-database"></a>Base de données Azure SQL unique

Pour restaurer une base de données unique ou mise en pool à l’aide d’Azure CLI, consultez [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Base de données d’instance managée

Pour restaurer une base de données d’instance managée à l’aide d’Azure CLI, consultez [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Résumé

Les sauvegardes automatiques protègent vos bases de données des erreurs utilisateur et des erreurs d’application, de la suppression accidentelle d’une base de données et des interruptions prolongées. Cette fonctionnalité intégrée est disponible pour tous les niveaux de service et tailles de calcul.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
- [Rétention à long terme](sql-database-long-term-retention.md)
- Pour découvrir des options de récupération plus rapides, consultez [Géoréplication active](sql-database-active-geo-replication.md) ou [Groupes de basculement automatique](sql-database-auto-failover-group.md).
