---
title: Sauvegardes Azure SQL Database automatiques ou géoredondantes | Microsoft Docs
description: SQL Database crée automatiquement une sauvegarde de base de données locale toutes les cinq minutes et utilise le stockage géoredondant avec accès en lecture pour fournir la géoredondance.
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
ms.date: 04/12/2019
ms.openlocfilehash: f0cff30f246bfeec528f440b507da9248ebbea9f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678596"
---
# <a name="automated-backups"></a>Sauvegardes automatisées

Base de données SQL est automatiquement crée des sauvegardes de base de données qui sont conservées entre 7 et 35 jours et utilise le stockage géo-redondant Azure avec accès en lecture (RA-GRS) pour vous assurer qu’elles sont conservées même si le centre de données n’est pas disponible. Ces sauvegardes sont créées automatiquement et sans frais supplémentaires. Vous n’avez rien à faire pour qu’elles se produisent et vous pouvez [modifier la période de conservation des sauvegardes](#how-to-change-the-pitr-backup-retention-period). Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Si les règles de sécurité nécessitent que vos sauvegardes soient disponibles pendant une période prolongée (jusqu’à 10 ans), vous pouvez configurer une stratégie de [conservation à long terme](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Qu’est-ce qu’une sauvegarde SQL Database ?

SQL Database utilise la technologie SQL Server pour créer des sauvegardes [complètes](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server), [différentielles](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) et du [journal des transactions](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) dans le cadre de la limite de restauration dans le temps. Les sauvegardes du journal des transactions se produisent généralement toutes les 5 à 10 minutes, et les sauvegardes différentielles toutes les 12 heures, la fréquence variant selon la taille de calcul et l’activité de la base de données. Les sauvegardes complètes et différentielles et les sauvegardes du journal des transactions vous permettent de restaurer une base de données à un moment spécifique sur le même serveur qui héberge la base de données. Les sauvegardes sont stockées dans des objets blob de stockage RA-GRS répliqués dans un [centre de données associé](../best-practices-availability-paired-regions.md) pour une protection contre une panne du centre de données. Quand vous restaurez une base de données, le service identifie les sauvegardes nécessitant une restauration (complète, différentielle ou journal des transactions).

Vous pouvez utiliser ces sauvegardes aux fins suivantes :

- Restaure une base de données à un point dans le temps dans la période de rétention. Cette opération crée une base de données dans le même serveur que la base de données d’origine.
- Restaurer une base de données supprimée sur le moment où elle été supprimée ou tout moment pendant la période de rétention. La base de données supprimée ne peut être restaurée que sur le même serveur où la base de données d’origine a été créée.
- Restaurer une base de données dans une autre région géographique. La géorestauration vous permet de procéder à la récupération après un sinistre géographique lorsque vous ne pouvez pas accéder à votre serveur, ni à la base de données. Cette opération crée une base de données sur n’importe quel serveur existant dans le monde entier.
- Restaurer une base de données à partir d’une sauvegarde à long terme spécifique si la base de données a été configurée avec une stratégie de rétention à long terme. La conservation à long terme vous permet de restaurer une ancienne version de la base de données pour répondre à une demande de conformité ou exécuter une ancienne version de l’application. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).
- Pour effectuer une restauration, consultez [Restauration de la base de données à partir de la sauvegarde](sql-database-recovery-using-backups.md).

> [!NOTE]
> Dans le stockage Azure, le terme *réplication* fait référence à la copie de fichier d’un emplacement à un autre. La *réplication de base de données* de SQL fait référence à la gestion de la synchronisation de plusieurs bases de données secondaires avec une base de données primaire.

## <a name="how-long-are-backups-kept"></a>Quelle est la durée de conservation des sauvegardes ?

Chaque base de données SQL Database a une période de rétention de sauvegarde par défaut comprise entre 7 et 35 jours qui dépend du modèle d’achat et du niveau de service. Vous pouvez mettre à jour de la période de rétention des sauvegardes pour une base de données sur le serveur de base de données SQL. Consultez [Modification de la période de conservation](#how-to-change-the-pitr-backup-retention-period) pour plus d’informations.

Si vous supprimez une base de données, SQL Database conserve les sauvegardes de la même façon que s’il s’agissait d’une base de données en ligne. Par exemple, si vous supprimez une base de données De base dont la période de conservation est de sept jours, une sauvegarde datant de quatre jours est enregistrée pendant encore trois jours.

Si vous souhaitez conserver les sauvegardes plus longtemps que la période de conservation maximale, vous pouvez modifier les propriétés de sauvegarde pour ajouter une ou plusieurs périodes de conservation à long terme à votre base de données. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Si vous supprimez le serveur Azure SQL Server qui héberge les bases de données SQL, tous les pools élastiques et bases de données qui appartiennent au serveur sont également supprimés, sans pouvoir être restaurés. Vous ne pouvez pas restaurer un serveur supprimé. Mais si vous avez configuré la conservation à long terme, les sauvegardes des bases de données avec conservation à long terme ne seront pas supprimées et peuvent être restaurées.

### <a name="default-backup-retention-period"></a>Période de conservation des sauvegardes par défaut

#### <a name="dtu-based-purchasing-model"></a>Modèle d’achat DTU

La période de conservation par défaut pour une base de données créée à l’aide du modèle d’achat DTU varie selon le niveau de service :

- Niveau de service De base : 1 jour.
- Niveau de service Standard : 5 semaines.
- Niveau de service Premium : 5 semaines.

#### <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore

Si vous utilisez le [modèle d’achat basé sur vCore](sql-database-service-tiers-vcore.md), la période de conservation des sauvegardes par défaut est de 7 jours (pour les bases de données uniques, regroupées et d'instances). Pour toutes les bases de données SQL Azure (uniques, regroupées et d'instances), vous pouvez [modifier la période de conservation des sauvegardes et la prolonger jusqu’à 35 jours](#how-to-change-the-pitr-backup-retention-period).

> [!WARNING]
> Si vous réduisez la période de rétention en cours, toutes les sauvegardes antérieures à la nouvelle période de rétention ne sont plus disponibles. Si vous augmentez la période de rétention actuelle, SQL Database conserve les sauvegardes existantes jusqu’à ce que la période de rétention plus longue soit atteinte.

## <a name="how-often-do-backups-happen"></a>À quelle fréquence les sauvegardes se produisent-elles ?

### <a name="backups-for-point-in-time-restore"></a>Sauvegardes pour une restauration dans le temps

SQL Database prend en charge la limite de restauration dans le temps en libre-service en créant automatiquement une sauvegarde complète, des sauvegardes différentielles et des sauvegardes du journal des transactions. Les sauvegardes de base de données complètes sont créées chaque semaine, les sauvegardes différentielles généralement toutes les 12 heures, et les sauvegardes de journal des transactions généralement toutes les 5 à 10 minutes, la fréquence variant selon la taille de calcul et l’activité de la base de données. La première sauvegarde complète est planifiée immédiatement après la création d’une base de données. Elle s’exécute généralement en 30 minutes, mais elle peut nécessiter davantage de temps s’il s’agit d’une base de données de taille considérable. Par exemple, la sauvegarde initiale peut prendre davantage de temps sur une base de données restaurée ou une copie de base de données. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées automatiquement et gérées en mode silencieux en arrière-plan. Le moment exact de toutes les sauvegardes de base de données est déterminé par le service SQL Database en fonction de l’équilibrage de la charge de travail globale du système. Vous ne pouvez pas modifier ou désactiver les travaux de sauvegarde. 

Les sauvegardes PITR sont géo-redondantes et protégées par la [réplication entre les régions du stockage Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Pour plus d'informations, consultez [Limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Sauvegardes pour la conservation à long terme

Les bases de données uniques et en pool permettent de configurer une conservation à long terme des sauvegardes complètes allant jusqu’à 10 ans dans le service Stockage Blob Azure. Si la stratégie de conservation à long terme est activée, les sauvegardes complètes hebdomadaires sont automatiquement copiées vers un autre conteneur de stockage RA-GRS. Pour répondre aux différentes exigences de conformité, vous pouvez sélectionner plusieurs périodes de rétention pour les sauvegardes hebdomadaires, mensuelles ou annuelles. La consommation du stockage dépend de la fréquence sélectionnée des sauvegardes et des périodes de conservation. Vous pouvez utiliser la [calculatrice de prix LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) pour estimer le coût du stockage de conservation à long terme.

Comme les sauvegardes PITR, les sauvegardes LTR sont géo-redondantes et protégées par la [réplication entre les régions du stockage Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Pour plus d’informations, consultez [Conservation des sauvegardes à long terme](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Coûts de stockage
7 jours de sauvegardes automatisées de vos bases de données sont copiés par défaut dans le stockage blob Standard RA-GRS. Le stockage est utilisé pour des sauvegardes complètes hebdomadaires, des sauvegardes différentielles quotidiennes et des sauvegardes de fichiers journaux copiés toutes les 5 minutes. La taille du journal des transactions dépend la fréquence de changement de la base de données. Un volume de stockage minimal égal à 100 % de la taille de la base de données est fourni sans frais supplémentaires. Toute consommation supérieure de stockage de sauvegarde est facturée en Go/mois.

Pour plus d’informations sur les prix du stockage, consultez la page [Tarification](https://azure.microsoft.com/pricing/details/sql-database/single/). 

## <a name="are-backups-encrypted"></a>Les sauvegardes sont-elles chiffrées ?

Si votre base de données est chiffrée à l’aide de TDE, les sauvegardes sont automatiquement chiffrées au repos, y compris les sauvegardes LTR. Lorsque le TDE est activé pour une base de données SQL Azure, les sauvegardes sont également chiffrées. Le TDE est configuré par défaut sur l’ensemble des nouvelles bases de données SQL Azure. Pour en savoir plus sur le TDE, consultez la page [Chiffrement transparent des données avec Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Comment Microsoft garantit l’intégrité de la sauvegarde ?

L’équipe d’ingénieurs Azure SQL Database teste régulièrement et automatiquement la restauration des sauvegardes automatisées de bases de données au sein du service. Lors de la restauration, les bases de données subissent également des vérifications d’intégrité à l’aide de DBCC CHECKDB. Tout problème détecté lors de la vérification d’intégrité est traduit par une alerte envoyée à l’équipe d’ingénieurs. Pour plus d’informations sur l’intégrité des données dans Azure SQL Database, consultez l’article [Intégrité des données dans Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Quel est l’impact des sauvegardes automatisées sur la conformité ?

Lorsque vous migrez votre base de données à partir d’un niveau de service basé sur DTU avec la conservation PITR par défaut de 35 jours, vers un niveau de service basé sur vCore, la conservation PITR est préservée pour garantir que la stratégie de récupération de données de votre application n’est pas compromise. Si la conservation par défaut ne répond pas à vos exigences de conformité, vous pouvez modifier la période de conservation PITR à l’aide de PowerShell ou de l’API REST. Consultez [Change Backup Retention Period](#how-to-change-the-pitr-backup-retention-period) (Modification de la période de conservation) pour plus d’informations.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Comment modifier la période de conservation des sauvegardes PITR ?

Vous pouvez modifier la période de rétention des sauvegardes jusqu'à une date par défaut à l’aide du portail Azure, PowerShell ou l’API REST. Les valeurs prises en charge sont les suivantes : 7, 14, 21, 28 ou 35 jours. Les exemples suivants illustrent comment modifier la conservation de récupération jusqu’à une date et heure pour la faire passer à 28 jours.

> [!NOTE]
> Ces API impactent uniquement la période de conservation PITR. Si vous avez configuré la conservation à long terme pour votre base de données, elle ne sera pas affectée. Consultez [Conservation à long terme](sql-database-long-term-retention.md) pour en savoir plus sur la modification des périodes de conservation à long terme.

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Modifier la période de conservation des sauvegardes PITR à l’aide du Portail Azure

Pour modifier la période de rétention des sauvegardes jusqu'à une date et à l’aide du portail Azure, accédez à l’objet serveur dont vous souhaitez modifier dans le portail, puis sélectionnez l’option appropriée la période de rétention basée sur quel objet de serveur que vous modifiez.

#### <a name="change-pitr-for-a-sql-database-server"></a>Modification de la récupération jusqu’à une date et heure d’un serveur SQL Database

![Modification PITR sur le Portail Azure](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Changer la valeur PITR d’une instance gérée

![Modification PITR sur le Portail Azure](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Modifier la période de conservation des sauvegardes PITR et à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Modifier la période de conservation PITR à l’aide de l’API REST

#### <a name="sample-request"></a>Exemple de demande

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Corps de la requête

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Exemple de réponse

Code d’état : 200

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

Pour plus d’informations, consultez [API REST de conservation des sauvegardes](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Étapes suivantes

- Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Pour en savoir plus sur les autres solutions de continuité des activités Azure SQL Database, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
- Pour effectuer une restauration à un point dans le temps à l’aide du portail Azure, consultez [Restauration d’une base de données à un point dans le temps à l’aide du portail Azure](sql-database-recovery-using-backups.md).
- Pour effectuer une restauration à un point dans le temps à l’aide de PowerShell, consultez [Restauration d’une base de données à un point dans le temps à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).
- Pour configurer, gérer et restaurer depuis la conservation à long terme de sauvegardes automatisées dans Stockage Blob Azure avec le portail Azure, consultez [Gestion de la rétention des sauvegardes à long terme à l’aide du Portail Azure](sql-database-long-term-backup-retention-configure.md).
- Pour configurer, gérer et restaurer depuis la rétention à long terme des sauvegardes automatisées dans le stockage Blob Azure à l’aide de PowerShell, consultez [gérer la rétention à long terme des sauvegardes à l’aide de PowerShell](sql-database-long-term-backup-retention-configure.md).
