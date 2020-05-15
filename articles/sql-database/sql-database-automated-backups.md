---
title: Sauvegardes géoredondantes automatiques
description: SQL Database crée automatiquement une sauvegarde de base de données locale toutes les cinq minutes et utilise le stockage géoredondant avec accès en lecture pour fournir la géoredondance.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 7cbe0015eeb9b46cd72496a220ce7f7d094cb61d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198570"
---
# <a name="automated-backups"></a>Sauvegardes automatisées

Azure SQL Database crée automatiquement les sauvegardes de base de données qui sont conservées pendant la période de rétention configurée. Il utilise un [stockage géographiquement redondant avec accès en lecture (RA-GRS)](../storage/common/storage-redundancy.md) pour s’assurer que les sauvegardes sont conservées même si le centre de distribution est indisponible.

Les sauvegardes de base de données sont une partie essentielle de toute stratégie de continuité d’activité ou de récupération d’urgence, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles. Si vos règles de sécurité nécessitent que vos sauvegardes soient disponibles pendant une période prolongée (jusqu’à 10 ans), vous pouvez configurer une stratégie de [conservation à long terme](sql-database-long-term-retention.md) dans des bases de données uniques et des pools de bases de données élastiques.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Qu’est-ce qu’une sauvegarde SQL Database ?

SQL Database utilise la technologie SQL Server pour créer des sauvegardes [complètes](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) (chaque semaine), [différentielles](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) (toutes les 12 heures) et du [journal des transactions](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) (toutes les 5 à 10 minutes). Les sauvegardes sont stockées dans des [objets blob de stockage RA-GRS](../storage/common/storage-redundancy.md) répliqués dans un [centre de données associé](../best-practices-availability-paired-regions.md) afin de proposer une protection contre toute panne du centre de données. Quand vous restaurez une base de données, le service identifie les sauvegardes (complète, différentielle ou du journal des transactions) nécessitant une restauration.

Vous pouvez utiliser ces sauvegardes aux fins suivantes :

- **Restaurer une base de données existante dans le temps**, à un moment situé pendant la période de rétention, à l’aide du portail Microsoft Azure, de Microsoft Azure PowerShell, de Microsoft Azure CLI ou de l’API REST. Pour les bases de données uniques et les pools de bases de données élastiques, cette opération crée une base de données au sein du même serveur que la base de données d’origine. Dans une instance gérée, cette opération peut créer une copie de la base de données, ou une instance gérée, identique ou non, pour le même abonnement.
- **Restaurer une base de données supprimée au moment de sa suppression** ou à tout moment de la période de rétention. La base de données supprimée ne peut être restaurée que sur le serveur logique ou l’instance gérée dans lesquels la base de données d’origine a été créée.
- **Restaurer une base de données dans une autre région géographique**. La géorestauration vous permet de procéder à la récupération après un sinistre géographique lorsque vous ne pouvez pas accéder à votre serveur, ni à la base de données. Cette opération crée une base de données sur un serveur existant quelconque n’importe où dans le monde.
- **Restaurer une base de données à partir d’une sauvegarde à long terme spécifique** dans une base de données unique ou un pool de bases de données élastique, si la base de données a été configurée avec une stratégie de conservation à long terme (LTR). La conservation à long terme (LTR) vous permet de restaurer une ancienne version de la base de données à l’aide du [portail Microsoft Azurel](sql-database-long-term-backup-retention-configure.md#using-azure-portal) ou de [Microsoft Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) pour répondre à une requête de conformité ou exécuter une ancienne version de l’application. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

Pour effectuer une restauration, consultez [Restaurer une base de données à partir de sauvegardes](sql-database-recovery-using-backups.md).

> [!NOTE]
> Dans Stockage Azure, le terme *réplication* fait référence à la copie de fichier d’un emplacement à un autre. La *réplication de base de données* SQL Server fait référence à la gestion de la synchronisation de plusieurs bases de données secondaires avec une base de données primaire.

Vous pouvez essayer certaines de ces opérations en utilisant les exemples suivants :

| | Le portail Azure | Azure PowerShell |
|---|---|---|
| Modifier la rétention des sauvegardes | [Base de données unique](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [instance gérée](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Base de données unique](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[instance gérée](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Modifier la rétention des sauvegardes à long terme | [Base de données unique](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Instance gérée – N/A  | [Base de données unique](sql-database-long-term-backup-retention-configure.md)<br/>Instance gérée – N/A  |
| Restaurer une base de données à un point dans le temps | [Base de données unique](sql-database-recovery-using-backups.md#point-in-time-restore) | [Base de données unique](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [instance gérée](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| La restauration d’une base de données supprimée | [Base de données unique](sql-database-recovery-using-backups.md) | [Base de données unique](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [instance gérée](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restaurer une base de données à partir d’un stockage d’objets blob Azure | Base de données unique - N/A <br/>Instance gérée – N/A  | Base de données unique - N/A <br/>[instance gérée](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Fréquence de sauvegarde

### <a name="point-in-time-restore"></a>Restauration dans le temps

SQL Database prend en charge la limite de restauration dans le temps en libre-service en créant automatiquement des sauvegardes complètes, des sauvegardes différentielles et des sauvegardes du journal des transactions. Les sauvegardes complètes de base de données sont créées chaque semaine et les sauvegardes différentielles sont généralement créées toutes les 12 heures. Les sauvegardes du journal de transactions sont généralement créées toutes les 5 à 10 minutes. La fréquence des sauvegardes du journal des transactions est basée sur la taille de calcul et le volume d’activité de la base de données. 

La première sauvegarde complète est planifiée immédiatement après la création d’une base de données. Elle s’exécute généralement en 30 minutes, mais elle peut nécessiter davantage de temps si la base de données est volumineuse. Par exemple, la sauvegarde initiale peut prendre davantage de temps sur une base de données restaurée ou une copie de base de données. Après la première sauvegarde complète, toutes les sauvegardes sont planifiées automatiquement et gérées en mode silencieux en arrière-plan. Le moment exact de toutes les sauvegardes de base de données est déterminé par le service SQL Database en fonction de l’équilibrage de la charge de travail globale du système. Vous ne pouvez pas modifier ou désactiver des travaux de sauvegarde.

### <a name="default-backup-retention-period"></a>Période de conservation des sauvegardes par défaut

Les sauvegardes avec récupération jusqu’à une date et heure sont protégées par un stockage géoredondant. Pour plus d’informations, consultez [Redondance de Stockage Azure](../storage/common/storage-redundancy.md).

Pour plus d’informations sur la restauration dans le temps, consultez [Restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Rétention à long terme

Pour des bases de données uniques et mises en pool, vous pouvez configurer une conservation à long terme (LTR) des sauvegardes complètes allant jusqu’à 10 ans dans un stockage d’objets blob Azure. Si vous activez une stratégie de conservation à long terme, les sauvegardes complètes hebdomadaires sont automatiquement copiées vers un autre conteneur de stockage RA-GRS. Pour répondre aux diverses exigences de conformité, vous pouvez sélectionner plusieurs périodes de rétention pour les sauvegardes hebdomadaires, mensuelles et/ou annuelles. La consommation du stockage dépend de la fréquence sélectionnée des sauvegardes et des périodes de rétention. Vous pouvez utiliser la [calculatrice de prix LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) pour estimer le coût du stockage de conservation à long terme.

Comme les sauvegardes avec récupération jusqu’à une date et heure, les sauvegardes avec conservation à long terme sont protégées par un stockage géoredondant. Pour plus d’informations, consultez [Redondance de Stockage Azure](../storage/common/storage-redundancy.md).

Pour plus d’informations sur la conservation à long terme, consultez [Conservation des sauvegardes à long terme](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Consommation de stockage de sauvegarde

Pour les bases de données uniques, l’équation utilisée pour calculer l’utilisation totale du stockage de sauvegarde est la suivante :

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Pour les pools de bases de données élastiques, la taille totale de stockage de sauvegarde est agrégée au niveau du pool et calculée comme suit :

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Les sauvegardes antérieures à la période de rétention sont automatiquement purgées en fonction de leur horodatage. Étant donné que les sauvegardes différentielles et les sauvegardes de fichiers journaux requièrent une sauvegarde complète antérieure, elles sont purgées en blocs hebdomadaires.

Azure SQL Database calcule votre stockage de sauvegarde total en rétention en tant que valeur cumulée. Toutes les heures, cette valeur est consignée dans le pipeline de facturation Azure, qui est responsable de l’agrégation de cette utilisation horaire afin de calculer votre consommation à la fin de chaque mois. Une fois la base de données supprimée, la consommation diminue à mesure que les sauvegardes vieillissent. Lorsque les sauvegardes atteignent un âge dépassant la période de rétention, la facturation s’arrête.

   > [!IMPORTANT]
   > Les sauvegardes d’une base de données sont conservées pendant la période de rétention spécifiée, même si la base de données a été supprimée. Si le fait de supprimer et de recréer une base de données peut fréquemment permettre d’économiser sur les coûts de stockage et de calcul, cela peut augmenter les coûts de stockage des sauvegardes, car Microsoft conserve une sauvegarde pendant la période de rétention spécifiée (sept jours au minimum) pour chaque base de données supprimée et à chaque suppression.

### <a name="monitor-consumption"></a>Surveiller la consommation

Chaque type de sauvegarde (complète, différentielle et de journal) est signalé sur le panneau de surveillance de la base de données sous la forme d’une mesure distincte. Le diagramme suivant montre comment surveiller la consommation de stockage des sauvegardes pour une base de données unique. Cette fonctionnalité n’est pas disponible actuellement pour les instances gérées.

![Surveiller la consommation de sauvegarde de base de données dans le portail Azure](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Ajuster la consommation de stockage de sauvegarde

Une consommation de stockage de sauvegarde excessive dépend de la charge de travail et de la taille des bases de données individuelles. Envisagez les diverses techniques d’ajustement suivantes pour réduire votre consommation de stockage de sauvegarde :

* Réduisez la [période de rétention de la sauvegarde](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) au minimum possible pour vos besoins.
* Évitez d’effectuer des opérations d’écriture volumineuses telles que des reconstructions d’index plus qu’il n’est nécessaire.
* Pour les opérations de chargement de données volumineuses, envisagez d’utiliser des [index columnstore en cluster](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), de réduire le nombre d’index non cluster et d’effectuer des opérations de chargement en masse avec environ 1 million de lignes.
* Au niveau de service à usage général, le stockage de données approvisionné est moins onéreux que le prix de l’excès de stockage de sauvegarde. Si vos coûts de stockage de sauvegarde sont sans cesse excessifs, vous pouvez envisager d’augmenter le stockage de données afin de réaliser des économies sur le stockage de sauvegarde.
* Utilisez TempDB au lieu de tables permanentes dans votre logique d’extraction, transformation et chargement (ETL) pour le stockage des résultats temporaires (applicable uniquement à l’instance gérée).
* Envisagez de désactiver le chiffrement TDE pour les bases de données qui ne contiennent pas de données sensibles (par exemple, bases de données de développement ou de test). Les sauvegardes des bases de données non chiffrées sont généralement compressées avec un taux de compression supérieur.

> [!IMPORTANT]
> Pour les charges de travail analytiques de mini-Data Warehouse/entrepôt de données, il est fortement recommandé d’utiliser des [index columnstore en cluster](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), de réduire le nombre d’index non cluster et de considérer les opérations de chargement en masse en prévoyant le traitement de 1 million de lignes environ afin de réduire la consommation de stockage de sauvegarde en excès.

## <a name="storage-costs"></a>Coûts de stockage

Le prix du stockage varie selon que vous utilisez le modèle DTU ou le modèle vCore.

### <a name="dtu-model"></a>Modèle DTU

Aucun frais supplémentaire n’est facturé pour le stockage de sauvegarde pour les bases de données et les pools de bases de données élastiques si vous utilisez le modèle DTU.

### <a name="vcore-model"></a>Modèle vCore

Pour les bases de données uniques, une quantité minimale de stockage de sauvegarde égale à 100 pour cent de la taille de la base de données est fournie sans frais supplémentaires. Pour les pools de bases de données élastiques et les instances managées, une quantité minimale de stockage de sauvegarde égale à 100 pour cent du stockage de données alloué respectivement pour le pool ou la taille d’instance est fournie sans frais supplémentaires. Toute consommation supérieure de stockage de sauvegarde est facturée en Go/mois. Cette consommation supplémentaire dépend de la charge de travail et de la taille des bases de données individuelles.

Azure SQL Database calcule votre stockage de sauvegarde total en rétention en tant que valeur cumulée. Toutes les heures, cette valeur est consignée dans le pipeline de facturation Azure, qui est responsable de l’agrégation de cette utilisation horaire afin d’obtenir votre consommation à la fin de chaque mois. Une fois la base de données supprimée, Microsoft réduit la consommation à mesure que les sauvegardes vieillissent. Lorsque les sauvegardes atteignent un âge dépassant la période de rétention, la facturation s’arrête. Étant donné que toutes les sauvegardes de fichier journal et les sauvegardes différentielles sont conservées pendant la période de rétention complète, les bases de données lourdement modifiées engendrent des frais de sauvegarde plus élevés.

Supposons que la base de données ait accumulé 744 Go de stockage de sauvegarde et que cette quantité reste constante pendant un mois entier. Pour convertir cette consommation de stockage cumulée en une utilisation horaire, nous la divisons par 744,0 (31 jours par mois * 24 heures par jour). Ainsi, SQL Database signale que la base de données a consommé 1 Go de sauvegarde avec récupération jusqu’à une date et heure par heure. La facturation Azure agrègera cette consommation et affichera une utilisation de 744 Go pour le mois. Le coût sera basé sur le tarif en $/Go/mois applicable dans votre région.

À présent, un exemple plus complexe. Supposons que la durée de rétention de la base de données est passée à 14 jours au milieu du mois. Supposons que cette augmentation (hypothétique) entraîne un doublement du stockage de sauvegarde total à 1 488 Go. SQL Database signale 1 Go d’utilisation pour les heures 1 à 372. Il signale l’utilisation de 2 Go pour les heures 373 à 744. L’agrégation de cette utilisation aboutit à une facturation finale de 1,116 Go/mois.

### <a name="monitor-costs"></a>Superviser les coûts

Pour comprendre les coûts de stockage des sauvegardes, accédez à **Gestion des coûts + Facturation** dans le portail Azure, sélectionnez **Gestion des coûts**, puis sélectionnez **Analyse du coût**. Sélectionnez l’abonnement souhaité comme **Étendue**, puis filtrez la période et le service qui vous intéressent.

Ajoutez un filtre pour **Nom de service**, puis choisissez **sql database** dans la liste déroulante. Utilisez le filtre **Meter subcategory** (Sous-catégorie du compteur) pour choisir le compteur de facturation pour votre service. Pour une base de données unique ou un pool de bases de données élastique, sélectionnez **single/elastic pool pitr backup storage**. Pour une instance gérée, sélectionnez **mi pitr backup storage**. Les sous-catégories **Stockage** et **Calcul** peuvent vous également intéresser, mais elles ne sont pas associées à des coûts de stockage de sauvegarde.

![Analyse du coût du stockage de sauvegarde](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Rétention des sauvegardes

Toutes les bases de données Azure SQL (uniques, en pool et d’instance gérée), ont une période de rétention de sauvegardes par défaut de 7 jours. Vous pouvez [modifier la période de rétention de sauvegardes](#change-the-pitr-backup-retention-period) en la portant jusqu’à 35 jours.

Si vous supprimez une base de données, SQL Database conserve les sauvegardes de la même façon que s’il s’agissait d’une base de données en ligne. Par exemple, si vous supprimez une base de données De base dont la période de rétention est de sept jours, une sauvegarde datant de quatre jours est enregistrée pendant trois jours supplémentaires.

Si vous souhaitez conserver les sauvegardes plus longtemps que la période de conservation maximale, vous pouvez modifier les propriétés de sauvegarde pour ajouter une ou plusieurs périodes de conservation à long terme à votre base de données. Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Si vous supprimez le serveur Azure SQL Server qui héberge les bases de données SQL, tous les pools de bases de données élastiques et bases de données qui appartiennent au serveur sont également supprimés. Il n’est pas possible de les récupérer. Vous ne pouvez pas restaurer un serveur supprimé. Mais si vous avez configuré la conservation à long terme, les sauvegardes des bases de données avec conservation à long terme ne seront pas supprimées et pourront être restaurées.

## <a name="encrypted-backups"></a>Sauvegardes chiffrées

Si votre base de données est chiffrée à l’aide de TDE, les sauvegardes sont automatiquement chiffrées au repos, y compris les sauvegardes LTR. Lorsque TDE est activé pour une base de données Azure SQL, les sauvegardes sont également chiffrées. TDE est configuré par défaut sur l’ensemble des nouvelles bases de données Azure SQL. Pour en savoir plus sur le TDE, consultez [Transparent Data Encryption avec Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Intégrité de la sauvegarde

L’équipe d’ingénieurs Azure SQL Database teste régulièrement et automatiquement la restauration des sauvegardes automatisées de bases de données placées sur des serveurs logiques et dans des pools de bases de données élastiques (ces tests ne sont pas disponible dans une instance gérée). Lors d’une restauration à un point dans le temps, les bases de données font également l’objet de vérifications d’intégrité DBCC CHECKDB.

Une instance gérée Instance effectue une sauvegarde initiale automatique avec `CHECKSUM` des bases de données restaurées à l’aide de la commande `RESTORE` native ou du service de migration des données Azure une fois la migration terminée.

Tout problème détecté lors de la vérification d’intégrité est traduit par une alerte envoyée à l’équipe d’ingénieurs. Pour plus d’informations, consultez [Intégrité des données dans Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Conformité

Lorsque vous migrez votre base de données à partir d’un niveau de service basé sur DTU vers un niveau de service basé sur vCore, la conservation PITR est préservée pour garantir que la stratégie de récupération de données de votre application ne soit pas compromise. Si la conservation par défaut ne répond pas à vos exigences de conformité, vous pouvez modifier la période de rétention PITR à l’aide de PowerShell ou de l’API REST. Pour plus d’informations, consultez [Modifier la période de rétention des sauvegardes PITR](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Modifier la période de rétention des sauvegardes PITR

Vous pouvez modifier la période de rétention des sauvegardes PITR par défaut à l’aide du portail Microsoft Azure, de PowerShell ou de l’API REST. Les exemples suivants illustrent comment modifier la conservation avec récupération jusqu’à une date et heure pour la faire passer à 28 jours.

> [!WARNING]
> Si vous réduisez la période de rétention actuelle, les sauvegardes plus anciennes que la nouvelle période de rétention ne seront plus disponibles. Si vous augmentez la période de rétention actuelle, SQL Database conserve les sauvegardes existantes jusqu’à ce que la fin de la période de rétention plus longue soit atteinte.

> [!NOTE]
> Ces API impactent uniquement la période de rétention avec récupération jusqu’à une date et heure. Si vous avez configuré la conservation à long terme pour votre base de données, cette configuration n’est pas affectée. Pour plus d’informations sur la modification des périodes de conservation à long terme, consultez [Conservation à long terme](sql-database-long-term-retention.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Modifier la période de rétention des sauvegardes avec récupération jusqu’à une date et heure via le portail Azure

Pour modifier la période de rétention des sauvegardes avec récupération jusqu’à une date et heure via le portail Azure, accédez à l’objet serveur dont vous souhaitez modifier la période de rétention dans le portail. Sélectionnez ensuite l’option appropriée en fonction de l’objet serveur que vous modifiez.

#### <a name="single-database-and-elastic-database-pools"></a>[Base de données unique et pools de bases de données élastiques](#tab/single-database)

Les changements de conservation des sauvegardes avec récupération jusqu’à une date et heure pour les bases de données Azure SQL uniques sont effectués au niveau du serveur. Les changements effectués au niveau du serveur s’appliquent aux bases de données figurant sur le serveur. Pour modifier la conservation des données avec récupération jusqu’à une date et heure pour un serveur Azure SQL Database à partir du portail Azure, accédez au panneau de vue d’ensemble du serveur. Sélectionnez **Gérer les sauvegardes** dans le volet gauche, puis **Configurer la rétention** en haut de l’écran :

![Modifier la conservation des données avec récupération jusqu’à une date et heure au niveau serveur](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[instance gérée](#tab/managed-instance)

La modification de la conservation des données avec récupération jusqu’à une date et heure pour une instance gérée SQL Database est effectuée au niveau d’une base de données individuelle. Pour modifier la conservation des données avec récupération jusqu’à une date et heure pour une base de données d’instance à partir du portail Azure, accédez au panneau de vue d’ensemble de la base de données. Sélectionnez ensuite **configurer la rétention des sauvegardes** en haut de l’écran :

![Modifier la conservation des données avec récupération jusqu’à une date et heure, instance gérée](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Modifier la période de conservation des données avec récupération jusqu’à une date et heure à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell AzureRM est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour plus d’informations, consultez la page [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az sont essentiellement identiques à ceux utilisés dans les modules AzureRm.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Modifier la période de rétention des sauvegardes avec récupération jusqu`à une date et heure à l’aide de l’API REST

#### <a name="sample-request"></a>Exemple de requête

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Corps de la demande

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
- Apprenez-en davantage sur la [restauration d’une base de données à un point dans le temps à l’aide du portail Azure](sql-database-recovery-using-backups.md).
- Apprenez-en davantage sur la [restauration d’une base de données à un point dans le temps à l’aide de PowerShell](scripts/sql-database-restore-database-powershell.md).
- Pour plus d’informations sur la façon de configurer, gérer et restaurer depuis la conservation à long terme de sauvegardes automatisées dans Stockage Blob Azure avec le portail Azure, consultez [Gestion de la rétention des sauvegardes à long terme à l’aide du Portail Azure](sql-database-long-term-backup-retention-configure.md).
- Pour plus d’informations sur la façon de configurer, gérer et restaurer des données à partir d’une conservation à long terme de sauvegardes automatisées dans Stockage Blob Azure avec PowerShell, voir [Gestion de la rétention des sauvegardes à long terme à l’aide de PowerShell](sql-database-long-term-backup-retention-configure.md).
