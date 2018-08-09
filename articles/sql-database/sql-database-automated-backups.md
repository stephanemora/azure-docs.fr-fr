---
title: Sauvegardes Azure SQL Database automatiques ou géoredondantes | Microsoft Docs
description: SQL Database crée automatiquement une sauvegarde de base de données locale toutes les cinq minutes et utilise le stockage géoredondant avec accès en lecture pour fournir la géoredondance.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: Active
ms.date: 07/25/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 1ddc663e6a7dc2d09a140b148c5297299d30d016
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39262845"
---
# <a name="learn-about-automatic-sql-database-backups"></a>En savoir plus sur les sauvegardes automatiques SQL Database

SQL Database crée automatiquement des sauvegardes de base de données et utilise le stockage géoredondant avec accès en lecture (RA-GRS) d’Azure pour fournir la géoredondance. Ces sauvegardes sont créées automatiquement et sans frais supplémentaires. Vous n’avez pas besoin de faire quoi que ce soit pour qu’elles se produisent. Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Si les règles de sécurité nécessitent que vos sauvegardes soient disponibles pendant une période prolongée, vous pouvez configurer une stratégie de rétention des sauvegardes à long terme. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Qu’est-ce qu’une sauvegarde SQL Database ?

SQL Database utilise la technologie SQL Server pour créer des sauvegardes [complètes](https://msdn.microsoft.com/library/ms186289.aspx), [différentielles](http://msdn.microsoft.com/library/ms175526.aspx) et du [journal des transactions](https://msdn.microsoft.com/library/ms191429.aspx) dans le cadre de la limite de restauration dans le temps. Les sauvegardes du journal des transactions se produisent généralement toutes les 5 à 10 minutes, et les sauvegardes différentielles toutes les 12 heures, la fréquence variant selon le niveau de performance et l’activité de base de données. Les sauvegardes du journal des transactions, avec les sauvegardes complètes et différentielles, vous permettent de restaurer une base de données à un point spécifique sur le même serveur qui héberge la base de données. Quand vous restaurez une base de données, le service identifie les sauvegardes nécessitant une restauration (complète, différentielle ou journal des transactions).


Vous pouvez utiliser ces sauvegardes aux fins suivantes :

* Restaure une base de données à un point dans le temps dans la période de rétention. Cette opération crée une base de données dans le même serveur que la base de données d’origine.
* Restaurer une base de données supprimée sur le moment où elle été supprimée ou tout moment pendant la période de rétention. La base de données supprimée ne peut être restaurée que sur le même serveur où la base de données d’origine a été créée.
* Restaurer une base de données dans une autre région géographique. Ceci vous permet de procéder à la récupération après un sinistre géographique lorsque vous ne pouvez pas accéder à votre serveur, ni à la base de données. Cette opération crée une base de données sur n’importe quel serveur existant dans le monde entier. 
* Restaurer une base de données à partir d’une sauvegarde à long terme spécifique si la base de données a été configurée avec une stratégie de rétention à long terme. Ceci vous permet de restaurer une ancienne version de la base de données pour répondre à une demande de conformité ou exécuter une ancienne version de l’application. Consultez [Rétention à long terme](sql-database-long-term-retention.md).
* Pour effectuer une restauration, consultez [Restauration de la base de données à partir de la sauvegarde](sql-database-recovery-using-backups.md).

> [!NOTE]
> Dans le stockage Azure, le terme *réplication* fait référence à la copie de fichier d’un emplacement à un autre. La *réplication de base de données* de SQL fait référence à la gestion de la synchronisation de plusieurs bases de données secondaires avec une base de données primaire. 
> 

## <a name="how-long-are-backups-kept"></a>Quelle est la durée de conservation des sauvegardes ?
Chaque sauvegarde SQL Database a une période de conservation par défaut basée sur le niveau de service de la base de données et diffère entre le [modèle d’achat DTU](sql-database-service-tiers-dtu.md) et le [modèle d’achat vCore](sql-database-service-tiers-vcore.md). Vous pouvez mettre à jour la période de conservation des sauvegardes pour une base de données. Consultez [Change Backup Retention Period](#how-to-change-backup-retention-period) (Modification de la période de conservation) pour plus d’informations.

Si vous supprimez une base de données, SQL Database conserve les sauvegardes de la même façon que s’il s’agissait d’une base de données en ligne. Par exemple, si vous supprimez une base de données De base dont la période de conservation est de sept jours, une sauvegarde datant de quatre jours est enregistrée pendant encore trois jours.

Si vous souhaitez conserver les sauvegardes plus longtemps que la période de conservation PITR maximale, vous pouvez modifier les propriétés de sauvegarde pour ajouter une ou plusieurs périodes de conservation à long terme à votre base de données. Consultez [Conservation des sauvegardes à long terme](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Si vous supprimez le serveur Azure SQL Server qui héberge les bases de données SQL, tous les pools élastiques et bases de données qui appartiennent au serveur sont également supprimés, sans pouvoir être restaurés. Vous ne pouvez pas restaurer un serveur supprimé. Mais si vous avez configuré la conservation à long terme, les sauvegardes des bases de données avec conservation à long terme ne seront pas supprimées et peuvent être restaurées.

### <a name="pitr-retention-for-dtu-based-service-tiers"></a>Conservation PITR pour les niveaux de service basés sur des unités de transaction de base de données
La période de conservation par défaut pour une base de données créée à l’aide du modèle d’achat DTU varie selon le niveau de service :

* Niveau de service De base : 1 jour.
* Niveau de service Standard : 5 semaines.
* Niveau de service Premium : 5 semaines.

Si vous réduisez la période de conservation PITR, toutes les sauvegardes antérieures à la nouvelle période de conservation ne seront plus disponibles. 

Si vous augmentez la période de conservation PITR, SQL Database conserve les sauvegardes existantes jusqu’à ce que la période de conservation plus longue soit atteinte.

## <a name="how-often-do-backups-happen"></a>À quelle fréquence les sauvegardes se produisent-elles ?
### <a name="backups-for-point-in-time-restore"></a>Sauvegardes pour une restauration dans le temps
SQL Database prend en charge la limite de restauration dans le temps en libre-service en créant automatiquement une sauvegarde complète, des sauvegardes différentielles et des sauvegardes du journal des transactions. Les sauvegardes de base de données complètes sont créées chaque semaine, les sauvegardes différentielles généralement toutes les 12 heures, et les sauvegardes de journal des transactions généralement toutes les 5 à 10 minutes, la fréquence variant selon le niveau de performance et l’activité de base de données. La première sauvegarde complète est planifiée immédiatement après la création d’une base de données. Elle s’exécute généralement en 30 minutes, mais elle peut nécessiter davantage de temps s’il s’agit d’une base de données de taille considérable. Par exemple, la sauvegarde initiale peut prendre davantage de temps sur une base de données restaurée ou une copie de base de données. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées automatiquement et gérées en mode silencieux en arrière-plan. Le moment exact de toutes les sauvegardes de base de données est déterminé par le service SQL Database en fonction de l’équilibrage de la charge de travail globale du système.

Les sauvegardes PITR sont géo-redondantes et protégées par la [réplication entre les régions du stockage Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Pour plus d'informations, consultez [Limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Sauvegardes pour la conservation à long terme
SQL Database permet de configurer une conservation à long terme des sauvegardes complètes d’une durée allant jusqu’à 10 ans dans Stockage Blob Azure. Si la stratégie de conservation à long terme est activée, les sauvegardes complètes hebdomadaires sont automatiquement copiées vers un autre conteneur de stockage RA-GRS. Pour répondre aux différentes exigences de conformité, vous pouvez sélectionner plusieurs périodes de rétention pour les sauvegardes hebdomadaires, mensuelles ou annuelles. La consommation du stockage dépend de la fréquence sélectionnée des sauvegardes et des périodes de conservation. Vous pouvez utiliser la [calculatrice de prix LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) pour estimer le coût du stockage de conservation à long terme. 

Comme les sauvegardes PITR, les sauvegardes LTR sont géo-redondantes et protégées par la [réplication entre les régions du stockage Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Pour plus d’informations, consultez [Conservation des sauvegardes à long terme](sql-database-long-term-retention.md).

## <a name="are-backups-encrypted"></a>Les sauvegardes sont-elles chiffrées ?

Si votre base de données est chiffrée à l’aide de TDE, les sauvegardes sont automatiquement chiffrées au repos, y compris les sauvegardes LTR. Lorsque le TDE est activé pour une base de données SQL Azure, les sauvegardes sont également chiffrées. Le TDE est configuré par défaut sur l’ensemble des nouvelles bases de données SQL Azure. Pour en savoir plus sur le TDE, consultez la page [Chiffrement transparent des données avec Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-do-automated-backups-impact-my-compliance"></a>Quel est l’impact des sauvegardes automatisées sur ma conformité ?

Lorsque vous migrez votre base de données à partir d’un niveau de service basé sur DTU avec la conservation PITR par défaut de 35 jours, vers un niveau de service basé sur vCore, la conservation PITR est préservée pour garantir que la stratégie de récupération de données de votre application n’est pas compromise. Si la conservation par défaut ne répond pas à vos exigences de conformité, vous pouvez modifier la période de conservation PITR à l’aide de PowerShell ou de l’API REST. Consultez [Change Backup Retention Period](#how-to-change-backup-retention-period) (Modification de la période de conservation) pour plus d’informations.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-backup-retention-period"></a>Modification de la période de conservation des sauvegardes
Vous pouvez modifier la conservation par défaut à l’aide de l’API REST ou de PowerShell. Les valeurs prises en charge sont : 7, 14, 21, 28 ou 35 jours. Les exemples suivants illustrent comment modifier la conservation de récupération jusqu’à une date et heure pour la faire passer à 28 jours. 

> [!NOTE]
> Ces API affectent uniquement la période de conservation PITR. Si vous avez configuré la conservation à long terme pour votre base de données, elle ne sera pas affectée. Consultez [Conservation des sauvegardes à long terme](sql-database-long-term-retention.md) pour en savoir plus sur la modification des périodes de conservation à long terme.

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Modifier la période de conservation des sauvegardes PITR et à l’aide de PowerShell
```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```
> [!IMPORTANT]
> Cette API est incluse dans AzureRM.Sql PowerShell Module depuis la version [4.7.0-préversion](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview). 

### <a name="change-pitr-retention-period-using-rest-api"></a>Modifier la période de conservation PITR à l’aide de l’API REST
**Exemple de demande**
```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```
**Corps de la requête**
```json
{
  "properties":{  
      "retentionDays":28
   }
}
```
**Exemple de réponse**

Code d’état : 200
```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```
Consultez [API REST de conservation des sauvegardes à long terme](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies) pour en savoir plus.

## <a name="next-steps"></a>Étapes suivantes

- Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Pour en savoir plus sur les autres solutions de continuité des activités Azure SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
- Pour effectuer une restauration à un point dans le temps à l’aide du portail Azure, consultez [Restauration d’une base de données à un point dans le temps à l’aide du portail Azure](sql-database-recovery-using-backups.md).
- Pour effectuer une restauration à un point dans le temps à l’aide de PowerShell, consultez [Restauration d’une base de données à un point dans le temps à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).
- Pour configurer, gérer et restaurer depuis la conservation à long terme de sauvegardes automatisées dans Stockage Blob Azure avec le portail Azure, consultez [Gestion de la rétention des sauvegardes à long terme à l’aide du Portail Azure](sql-database-long-term-backup-retention-configure.md).
- Pour configurer, gérer et restaurer depuis la conservation à long terme de sauvegardes automatisées dans Stockage Blob Azure avec PowerShell, consultez [Gestion de la conservation des sauvegardes à long terme avec PowerShell](sql-database-long-term-backup-retention-configure.md).
