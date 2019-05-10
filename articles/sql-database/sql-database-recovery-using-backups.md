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
manager: craigg
ms.date: 04/30/2019
ms.openlocfilehash: 47bf59adb33f3685b31430c652b31880d383833e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232646"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Récupérer une base de données Azure SQL à l’aide des sauvegardes automatisées d’une base de données

Par défaut, les sauvegardes de bases de données SQL sont placées dans l'espace de stockage blob géographiquement redondant (RA-GRS). Les options suivantes sont disponibles pour la récupération des bases de données à l’aide des [sauvegardes de bases de données automatisées](sql-database-automated-backups.md) :

- Créer une base de données sur le même serveur SQL Database récupéré à un point spécifié dans le temps durant la période de rétention.
- Créer une base de données sur le même serveur SQL Database récupéré à l’heure de suppression pour une base de données supprimée.
- Créer une base de données sur un serveur SQL Database dans la même région récupérée au point des sauvegardes les plus récentes.
- Créer une base de données sur un serveur SQL Database dans n’importe quelle autre région récupérée au point des sauvegardes répliquées les plus récentes.

Si vous avez configuré [la rétention à long terme des sauvegardes](sql-database-long-term-retention.md), vous pouvez également créer une nouvelle base de données à partir de n’importe quelle sauvegarde de rétention à long terme sur n’importe quel serveur de base de données SQL.

> [!IMPORTANT]
> Vous ne pouvez pas remplacer une base de données existante lors de la restauration.

Lorsque vous utilisez le niveau de service Standard ou Premium, une base de données restaurée implique un coût de stockage supplémentaire dans les conditions suivantes :

- La restauration de P11-P15 vers S4-S12 ou P1-P6 si la taille maximale de la base de données est supérieure à 500 Go.
- La restauration de P1-P6 vers S4-S12 si la taille maximale de la base de données est supérieure à 250 Go.

Supplémentaire coût est icurred lorsque la taille maximale de la base de données restaurée est supérieure à la quantité de stockage inclus avec le niveau de niveaux de performance et de service de la base de données cible. L’espace de stockage supplémentaire approvisionné en plus le montant inclus est facturé en plus. Pour les détails de la tarification du stockage supplémentaire, consultez la page [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). Si la quantité réelle d’espace utilisé est inférieure à la quantité de stockage inclus, vous pouvez éviter ce coût supplémentaire en définissant la taille maximale de la base de données à la quantité incluse.

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

Pour une base de données de grande taille et/ou très active, la restauration peut prendre plusieurs heures. En cas de panne prolongée dans une région, il est possible qu’un grand nombre de demandes de géorestauration soient traitées par d’autres régions. S’il y a un grand nombre de demandes, le temps de récupération des bases de données de cette région peut s’en trouver augmenté. La plupart des restaurations de bases de données s’effectuent au moins de 12 heures.

Pour un seul abonnement, voici les limitations sur le nombre de demandes de restauration simultanées.  Ces limitations s’appliquent à n’importe quelle combinaison de point dans le temps restaurations, des restaurations de géographique et des restaurations à partir de la sauvegarde de rétention à long terme) :

| | **Nombre maximum de requêtes simultanées traitées** | **Nombre maximum de requêtes simultanées soumises** |
| :--- | --: | --: |
|Base de données unique (par abonnement)|10|60|
|Pool élastique (par pool)|4|200|
||||

Actuellement il n’existe pas de méthode intégrée permettant de restaurer l’ensemble du serveur. Le script [Azure SQL Database : Total de récupération du serveur](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script est un exemple de comment vous pouvez accomplir cette tâche.

> [!IMPORTANT]
> Pour effectuer une récupération à l’aide de sauvegardes automatisées, vous devez avoir un rôle de collaborateur SQL Server dans l’abonnement ou être le propriétaire de l’abonnement. Voir [Rôles intégrés pour les ressources Azure](../role-based-access-control/built-in-roles.md). Vous pouvez effectuer une récupération en utilisant le portail Azure, PowerShell ou l’API REST. Vous ne pouvez pas utiliser Transact-SQL.

## <a name="point-in-time-restore"></a>Limite de restauration dans le temps

Vous pouvez restaurer un autonome, mis en pool, ou de l’instance de base de données à un point antérieur dans le temps à l’aide du portail Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), ou le [API REST](https://docs.microsoft.com/rest/api/sql/databases). La demande peut spécifier n’importe quel niveau de service ou calculer la taille de la base de données restaurée. Assurez-vous d’avoir suffisamment de ressources sur le serveur vers lequel vous restaurez la base de données. Une fois terminé, une nouvelle base de données va être créé sur le même serveur que la base de données d’origine. La base de données restaurée sera facturée au tarif normal en fonction de son niveau de service et la taille de calcul. Aucun frais ne vous sera facturé jusqu’à ce que la restauration de la base de données soit terminée.

En règle générale, vous restaurez une base de données à un point antérieur à des fins de récupération. Vous pouvez traiter la base de données restaurée comme remplacement pour la base de données d’origine ou utilisez-le comme une source de données pour mettre à jour de la base de données d’origine.

- **Remplacement de la base de données**

  Si la base de données restaurée est destinée à remplacer la base de données d’origine, vous devez spécifier la taille de calcul de la base de données orinal et niveau de service. Vous pouvez ensuite renommer la base de données d’origine et donnez à la base de données restaurée le nom d’origine en utilisant le [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) commande T-SQL.

- **Récupération des données**

  Si vous envisagez de récupérer des données à partir de la base de données restaurée suite à une erreur utilisateur ou une application, vous devez écrire et exécuter un script de récupération de données qui extrait des données à partir de la base de données restaurée et s’applique à la base de données d’origine. Bien que l’opération de restauration puisse prendre un certain temps, la base de données en cours de restauration sera visible dans la liste de bases de données pendant tout le processus de restauration. Si vous supprimez la base de données pendant la restauration, l’opération de restauration sera annulée et vous ne serez pas facturé pour la base de données qui n’a pas la restauration.

Pour récupérer une base de données unique, mise en pool ou d'instance à un moment spécifique à l’aide du portail Azure, ouvrez la page de votre base de données, puis cliquez sur **Restaurer** dans la barre d’outils.

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Pour restaurer par programmation une base de données à partir d’une sauvegarde, consultez [Exécution par programme d’une récupération à l’aide des sauvegardes automatisées](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups).

## <a name="deleted-database-restore"></a>Restauration d’une base de données supprimée

Vous pouvez restaurer une base de données supprimée à l’heure de suppression ou d’un point antérieur dans le temps sur le même serveur de base de données SQL à l’aide du portail Azure, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), ou le [REST (createMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Vous pouvez [restaurer une base de données supprimée sur Managed Instance à l’aide de PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Pour obtenir un exemple de script PowerShell montrant comment restaurer une base de données supprimée, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Si vous supprimez une instance de serveur Azure SQL Database, toutes ses bases de données sont également supprimées et ne peuvent pas être récupérées. Il n'existe aucune prise en charge pour la restauration d'un serveur supprimé pour l'instant.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Restauration d’une base de données supprimée à l’aide du portail Azure

Pour récupérer une base de données supprimée à l’aide du portail Azure, ouvrez la page de votre serveur et dans la zone opérations, cliquez sur **bases de données supprimées**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Pour restaurer par programmation une base de données supprimée, consultez [Exécution par programme d’une récupération à l’aide des sauvegardes automatisées](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups).

## <a name="geo-restore"></a>Géo-restauration

Vous pouvez restaurer une base de données SQL sur n’importe quel serveur dans n’importe quelle région Azure à partir de la dernière sauvegarde géo-répliquée. Géo-restauration utilise une sauvegarde géo-répliquée en tant que sa source. Il peut être demandé même si la base de données ou d’un centre de données est inaccessible en raison d’une panne.

Géo-restauration constitue l’option de récupération par défaut lorsque votre base de données est indisponible en raison d’un incident dans la région d’hébergement. Vous pouvez restaurer la base de données à un serveur dans n’importe quelle autre région. Il peut y avoir un délai entre le moment où la sauvegarde est effectuée et celui où elle est géo-répliquée dans un objet blob Azure dans une autre région. Par conséquent, la base de données restaurée peut être jusqu'à une heure derrière la base de données originale. L’illustration ci-dessous illustre la restauration de la base de données à partir de la dernière sauvegarde disponible dans une autre région.

![Restauration géographique](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Pour obtenir un exemple de script PowerShell montrant comment effectuer une géorestauration, consultez [Restaurer une base de données SQL à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).

La restauration dans le temps sur un géo-réplica secondaire n’est pas prise en charge actuellement. La restauration dans le temps peut être effectuée uniquement sur une base de données primaire. Pour plus d’informations sur l’utilisation de la géorestauration pour la récupération suite à une panne, voir [Récupération après une panne](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Géo-restauration est la solution de récupération d’urgence plus basique disponible dans la base de données SQL. Il s’appuie sur créé automatiquement des sauvegardes répliquées géographiquement avec RPO = 1 heure et la durée estimée de récupération de 12 heures. Il ne garantit pas que la région cible aura la capacité à restaurer vos bases de données après un ourage régional, car une brusque hausse de la demande sera probablement. Pour les non critiques application métier qui utilisent des bases de données relativement petites, géo-restauration est une solution de récupération d’urgence appropriée. Pour les applications critiques social qui utilisent des bases de données volumineuses et doivent garantir un fonctionnement ininterrompu, vous devez utiliser [groupes de basculement automatique](sql-database-auto-failover-group.md). Il offre un RPO et RTO beaucoup plus faible, et la capacité est toujours garantie. Pour plus d’informations sur les choix de continuité d’activité, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

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

Pour restaurer une base de données unique ou mise en pool à l’aide de l’API REST :

| API | Description |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Restaure une base de données |
| [Créer ou mettre à jour l’état de la base de données](https://docs.microsoft.com/rest/api/sql/operations) |Retourne l’état durant une opération de restauration |

### <a name="azure-cli"></a>Azure CLI

- Pour restaurer une base de données unique ou mise en pool à l’aide d’Azure CLI, consultez [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).
- Pour restaurer une instance gérée à l’aide d’Azure CLI, consultez [restauration de midb az sql](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Résumé

Les sauvegardes automatiques protègent vos bases de données des erreurs utilisateur et des erreurs d’application, de la suppression accidentelle d’une base de données et des interruptions prolongées. Cette fonctionnalité intégrée est disponible pour tous les niveaux de service et tailles de calcul.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
- Pour en savoir plus sur les sauvegardes automatisées d’une base de données Azure SQL, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
- Pour plus d’informations sur la rétention à long terme, consultez [Rétention à long terme](sql-database-long-term-retention.md).
- Pour découvrir des options de récupération plus rapides, consultez [Géoréplication active](sql-database-active-geo-replication.md) ou [Groupes de basculement automatique](sql-database-auto-failover-group.md).
