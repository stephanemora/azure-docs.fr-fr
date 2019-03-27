---
title: Sauvegarde et restauration d’Azure SQL Data Warehouse – instantanés, géoredondants | Microsoft Docs
description: Découvrez comment la sauvegarde et la restauration fonctionnent dans Azure SQL Data Warehouse. Utilisez des sauvegardes d’entrepôts de données pour restaurer votre entrepôt de données à un point de restauration dans la région primaire. Utilisez des sauvegardes géoredondantes pour restaurer dans une autre région géographique.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/01/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 55874d261ac453d559975f25b2272319cdc6a7db
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448003"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Sauvegarde et restauration dans Azure SQL Data Warehouse

Découvrez comment utiliser la sauvegarde et restauration dans Azure SQL Data Warehouse. Utilisez des points de restauration d'entrepôt de données pour récupérer ou copier votre entrepôt de données à un état précédent dans la région primaire. Utilisez des sauvegardes géoredondantes d’entrepôts de données pour effectuer une restauration dans une autre région géographique.

## <a name="what-is-a-data-warehouse-snapshot"></a>Qu’est un instantané de l’entrepôt de données

Une *capture instantanée d’entrepôt de données* crée un point de restauration que vous pouvez utiliser pour récupérer ou copier votre entrepôt de données dans un état antérieur.  Comme SQL Data Warehouse est un système distribué, une capture instantanée d’entrepôt de données est constituée de nombreux fichiers qui sont stockés dans le stockage Azure. Les captures instantanées capturent les changements incrémentiels à partir des données stockées dans votre entrepôt de données.

Une *restauration d’entrepôt de données* est un nouvel entrepôt de données créé à partir d’un point de restauration d’un entrepôt de données existant ou supprimé. La restauration de votre entrepôt de données est une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elle recrée vos données après des corruptions et des suppressions accidentelles. Un entrepôt de données est également un mécanisme puissant pour créer des copies de votre entrepôt de données à des fins de test ou de développement.  SQL Data Warehouse utilise des mécanismes de restauration rapide dans la même région où le temps de restauration mesuré est inférieur à 20 minutes pour n’importe quelle taille de données.

## <a name="automatic-restore-points"></a>Points de restauration automatiques

Les instantanés sont une fonctionnalité intégrée du service qui crée des points de restauration. Il n’est pas nécessaire de l’activer. Les points de restauration automatiques ne peuvent actuellement pas être supprimés par les utilisateurs dans les emplacements où le service utilise ces points de restauration pour conserver les contrats SLA pour la récupération.

SQL Data Warehouse prend des captures instantanées de votre entrepôt de données pendant la journée en créant des points de restauration qui sont disponibles pendant sept jours. Cette période de conservation ne peut pas être modifiée. SQL Data Warehouse prend en charge un objectif de point de récupération (RPO) de huit heures. Vous pouvez restaurer votre entrepôt de données dans la région primaire à partir de n’importe quelle capture instantanée prise au cours des sept derniers jours.

Pour voir quand la dernière capture instantanée a démarré, exécutez cette requête sur votre entrepôt de données SQL Data Warehouse en ligne.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Points de restauration définis par l’utilisateur

Cette fonctionnalité vous permet de manuellement des instantanés de déclencheur pour créer des points de restauration de votre entrepôt de données avant et après des modifications importantes apportées. Cette fonctionnalité garantit que les points de restauration sont logiquement cohérentes, ce qui fournit une protection des données supplémentaires dans le cas des interruptions de la charge de travail ou des erreurs de l’utilisateur pour le temps de récupération rapide. Les points de restauration définis par l’utilisateur sont disponibles pendant sept jours et sont automatiquement supprimés pour votre compte. Vous ne pouvez pas changer la période de conservation des points de restauration définis par l’utilisateur. **42 points de restauration définis par l’utilisateur** sont garantis à un instant donné. Ils doivent donc être [supprimés](https://go.microsoft.com/fwlink/?linkid=875299) avant qu’un autre point de restauration soit créé. Vous pouvez déclencher des captures instantanées pour créer des points de restauration définis par l’utilisateur par le biais de [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoint#examples) ou du portail Azure.

> [!NOTE]
> Si vous avez besoin de points de restauration de plus de 7 jours, votez pour cette fonctionnalité [ici](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). Vous pouvez également créer un point de restauration défini par l’utilisateur et effectuer une restauration à partir du point de restauration nouvellement créé dans un nouvel entrepôt de données. Une fois la restauration effectuée, vous retrouvez l’entrepôt de données en ligne que vous pouvez mettre en pause indéfiniment pour réduire les coûts de calcul. La base de données en pause entraîne des frais de stockage aux tarifs du Stockage Premium Azure. Si vous avez besoin d’une copie active de l’entrepôt de données restauré, vous pouvez reprendre, ce qui ne doit prendre que quelques minutes.

### <a name="restore-point-retention"></a>Conservation des points de restauration

Les détails suivants de listes pour les périodes de rétention de point de restauration :

1. SQL Data Warehouse supprime un point de restauration quand celui-ci atteint la période de conservation de 7 jours **et** lorsqu’il y a au moins 42 points de restauration au total (points de restauration définis par l’utilisateur et points de restauration automatiques)
2. Aucune capture instantanée n’est prise lorsqu’un entrepôt de données est mis en pause
3. L’âge d’un point de restauration est mesuré selon les jours calendaires absolus à partir du moment où le point de restauration est pris, y compris lorsque l’entrepôt de données est mis en pause
4. À tout moment, un entrepôt de données peut stocker jusqu’à 42 points de restauration définis par l’utilisateur et 42 points de restauration automatiques, à condition que ces points de restauration n’aient pas atteint la période de conservation de 7 jours
5. Si une capture instantanée est prise, l’entrepôt de données est alors mis en pause pendant plus de 7 jours, puis son activité reprend. Il est possible que le point de restauration persiste jusqu’à ce qu’il y ait 42 points de restauration au total (points de restauration définis par l’utilisateur et points de restauration automatiques)

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>Rétention des captures instantanées lorsqu’un entrepôt de données est supprimé

Lorsque vous supprimez un entrepôt de données, SQL Data Warehouse crée une capture instantanée finale et l’enregistre pendant sept jours. Vous pouvez restaurer l’entrepôt de données au point de restauration final créé lors de la suppression.

> [!IMPORTANT]
> Si vous supprimez une instance SQL Server logique, toutes les bases de données qui appartiennent à l’instance sont également supprimées, sans pouvoir être récupérées. Vous ne pouvez pas restaurer un serveur supprimé.

## <a name="geo-backups-and-disaster-recovery"></a>Sauvegardes de géoréplication et récupération d’urgence

SQL Data Warehouse effectue une géosauvegarde une fois par jour vers un [centre de données couplé](../best-practices-availability-paired-regions.md). Le RPO pour une géo-restauration est de 24 heures. Vous pouvez restaurer la géo-sauvegarde sur un serveur dans n’importe quelle autre région où SQL Data Warehouse est pris en charge. Une géosauvegarde vous garantit de pouvoir restaurer un entrepôt de données dans le cas où vous ne pouvez pas accéder aux points de restauration de votre région primaire.

Les géosauvegardes sont activées par défaut. Si votre entrepôt de données est de type Gen1, vous pouvez les [désactiver](/powershell/module/az.sql/set-azsqldatabasegeobackuppolicy) si vous le souhaitez. Vous ne pouvez pas désactiver les géosauvegardes pour Gen2, car la protection des données est une garantie intégrée.

> [!NOTE]
> Si vous avez besoin d’un objectif de point de récupération (RPO) plus court pour les géosauvegardes, votez pour cette fonctionnalité [ici](https://feedback.azure.com/forums/307516-sql-data-warehouse). Vous pouvez également créer un point de restauration défini par l’utilisateur et effectuer une restauration à partir du point de restauration nouvellement créé dans un nouvel entrepôt de données se trouvant dans une autre région. Une fois la restauration effectuée, vous retrouvez l’entrepôt de données en ligne que vous pouvez mettre en pause indéfiniment pour réduire les coûts de calcul. La base de données en pause entraîne des frais de stockage aux tarifs du Stockage Premium Azure. Si vous avez besoin d’une copie active de l’entrepôt de données, vous pouvez reprendre, ce qui ne doit prendre que quelques minutes.

## <a name="backup-and-restore-costs"></a>Coûts de sauvegarde et de restauration

Vous remarquerez que la facture Azure comprend un élément de ligne pour le Stockage et un élément de ligne pour le Stockage de reprise d’activité après sinistre. Les frais de stockage correspondent au coût total du stockage de vos données dans la région principale et aux changements incrémentiels pris par des captures instantanés. Pour en savoir plus sur la facturation des captures instantanées, reportez-vous à [Facturation des captures instantanées](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios). Les frais géoredondants couvrent le coût du stockage des géosauvegardes.  

Le coût total de votre entrepôt de données principal et de sept jours de changements de captures instantanées est arrondi au To le plus proche. Par exemple, si la taille de votre entrepôt de données est de 1,5 To et que les captures instantanées occupent 100 Go, vous êtes facturé pour 2 To de données aux prix du Stockage Premium Azure.

Si vous utilisez le stockage géoredondant, vous payez des frais de stockage distincts. Le stockage géoredondant est facturé au prix standard du stockage géoredondant avec accès en lecture (RA-GRS).

Pour plus d’informations sur la tarification de SQL Data Warehouse, consultez [Tarification de SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) et [Frais de sortie](https://azure.microsoft.com/pricing/details/bandwidth/) lors de la restauration inter-région.

## <a name="restoring-from-restore-points"></a>Restauration à partir de points de restauration

Chaque capture instantanée crée un point de restauration qui représente son heure de début. Pour restaurer un entrepôt de données, choisissez un point de restauration et émettez une commande de restauration.  

Vous pouvez conserver les entrepôts de données restauré et actuel ou les supprimer tous les deux. Si vous souhaitez remplacer l’entrepôt de données actuel par l’entrepôt de données restauré, vous pouvez le renommer à l’aide d’[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) avec l’option MODIFIER LE NOM.

Pour restaurer un entrepôt de données, consultez [Restaurer un entrepôt de données à l’aide du portail Azure](sql-data-warehouse-restore-database-portal.md), [Restaurer un entrepôt de données à l’aide de PowerShell](sql-data-warehouse-restore-database-powershell.md) ou [Restaurer un entrepôt de données à l’aide d’API REST](sql-data-warehouse-restore-database-rest-api.md).

Si vous devez restaurer un entrepôt de données supprimé ou suspendu, vous pouvez [créer un ticket de support](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Cross-restauration de l’abonnement

Si vous devez restaurer directement d’abonnement, votez pour cette fonctionnalité [ici](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Restaurer vers un autre serveur logique et [« Déplacer »](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) le serveur entre des abonnements pour effectuer une restauration entre abonnements. 

## <a name="geo-redundant-restore"></a>Restauration géoredondante

Vous pouvez [restaurer votre entrepôt de données](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) dans n’importe quelle région prenant en charge SQL Data Warehouse au niveau de performances que vous avez choisi.

> [!NOTE]
> Pour effectuer une restauration géoredondante, vous devez avoir activé cette fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la planification de la récupération d’urgence, consultez [Vue d’ensemble de la continuité de l’activité](../sql-database/sql-database-business-continuity.md).
