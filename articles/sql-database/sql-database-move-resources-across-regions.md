---
title: Comment déplacer des ressources vers une autre région
description: Découvrez comment déplacer votre base de données Azure SQL Database, votre pool élastique Azure SQL ou votre instance managée Azure SQL vers une autre région.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821432"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Guide pratique pour déplacer des ressources Azure SQL vers une autre région

Cet article présente un flux de travail générique pour déplacer une base de données unique Azure SQL Database, un pool élastique et une instance managée vers une nouvelle région. 

## <a name="overview"></a>Vue d’ensemble

Il existe différents scénarios dans lesquels vous pourriez souhaiter déplacer vos ressources Azure SQL existantes d’une région à une autre. Par exemple, vous étendez vos activités à une nouvelle région et souhaitez les optimiser pour la nouvelle base de clients. Ou vous avez besoin de déplacer les opérations vers une autre région pour des raisons de conformité. Ou encore, Azure a publié une nouvelle région qui offre une meilleure proximité et améliore l’expérience client.  

Cet article fournit un flux de travail général pour déplacer des ressources vers une autre région. Le flux de travail se déroule comme suit : 

- Vérifier les prérequis pour le déplacement 
- Préparer le déplacement des ressources dans l’étendue
- Superviser le processus de préparation
- Tester le processus de déplacement
- Lancer le déplacement 
- Supprimer les ressources de la région source 


> [!NOTE]
> Cet article s’applique aux migrations dans le cloud public Azure ou dans le même cloud souverain. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Déplacer une base de données unique

### <a name="verify-prerequisites"></a>Vérifier la configuration requise 

1. Créez un serveur logique cible pour chaque serveur source. 
1. Configurez le pare-feu avec les exceptions appropriées à l’aide de [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Configurez les serveurs logiques avec les connexions appropriées. Si vous n’êtes pas administrateur de l’abonnement ou administrateur SQL Server, collaborez avec l’administrateur pour affecter les autorisations dont vous avez besoin. Pour plus d’informations, consultez [Guide pratique pour gérer la sécurité de la base de données Azure SQL après la reprise d’activité](sql-database-geo-replication-security-config.md). 
1. Si vos bases de données sont chiffrées avec TDE et que vous utilisez votre propre clé de chiffrement dans Azure Key Vault, vérifiez que le matériel de chiffrement correct est provisionné dans les régions cibles. Pour plus d’informations, consultez [Azure SQL Transparent Data Encryption avec des clés managées dans Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Si l’audit au niveau de la base de données est activé, désactivez-le et activez plutôt l’audit au niveau du serveur. Après le basculement, l’audit au niveau de la base de données nécessitera le trafic inter-régions, qui n’est pas souhaitable ou possible après le déplacement. 
1. Pour les audits au niveau du serveur, vérifiez que :
   - Le conteneur de stockage, Log Analytics ou le hub d’événements avec les journaux d’audit existants est déplacé vers la région cible. 
   - L’audit est configuré sur le serveur cible. Pour en savoir plus, voir [Prise en main de l’audit de base de données SQL](sql-database-auditing.md). 
1. Si votre instance a une stratégie de conservation à long terme (LTR), les sauvegardes LTR existantes resteront associées au serveur actuel. Le serveur cible étant différent, vous pourrez accéder aux sauvegardes LTR plus anciennes dans la région source à l’aide du serveur source, même si le serveur est supprimé. 

  > [!NOTE]
  > Cela ne sera pas suffisant pour les déplacements entre le cloud souverain et une région publique. Pour une telle migration, vous devrez déplacer les sauvegardes LTR vers le serveur cible, ce qui n’est pas pris en charge actuellement. 

### <a name="prepare-resources"></a>Préparer les ressources

1. Créez un [groupe de basculement](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) entre le serveur logique de la source et le serveur logique de la cible.  
1. Ajoutez les bases de données que vous souhaitez déplacer au groupe de basculement. 
    - La réplication de toutes les bases de données ajoutées sera lancée automatiquement. Pour plus d’informations, consultez [Meilleures pratiques relatives à l’utilisation des groupes de basculement avec des bases de données uniques et des pools élastiques](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Superviser le processus de préparation

Vous pouvez appeler régulièrement [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) pour superviser la réplication de vos bases de données de la source vers la cible. L’objet de sortie de `Get-AzSqlDatabaseFailoverGroup` comprend une propriété pour **ReplicationState** : 
   - **ReplicationState = 2** (CATCH_UP) indique que la base de données est synchronisée et peut être basculée en toute sécurité. 
   - **ReplicationState = 0** (SEEDING) indique que la base de données n’est pas encore amorcée et qu’une tentative de basculement échouera. 

### <a name="test-synchronization"></a>Tester la synchronisation

Une fois que **ReplicationState** a la valeur `2`, connectez-vous à chaque base de données ou sous-ensemble de bases de données à l’aide du point de terminaison secondaire `<fog-name>.secondary.database.windows.net` et exécutez une requête sur les bases de données pour vérifier la connectivité, la configuration de sécurité et la réplication des données. 

### <a name="initiate-the-move"></a>Lancer le déplacement

1. Connectez-vous au serveur cible à l’aide du point de terminaison secondaire `<fog-name>.secondary.database.windows.net`.
1. Utilisez [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) pour faire de l’instance managée secondaire la base de données primaire avec synchronisation complète. Cette opération réussira ou sera annulée. 
1. Vérifiez que la commande s’est correctement exécutée en exécutant `nslook up <fog-name>.secondary.database.windows.net` pour vérifier que l’entrée CNAME DNS pointe vers l’adresse IP de la région cible. Si la commande Switch échoue, le CNAME ne sera pas mis à jour. 

### <a name="remove-the-source-databases"></a>Supprimer les bases de données sources

Une fois le déplacement terminé, supprimez les ressources dans la région source pour éviter les frais inutiles. 

1. Supprimez le groupe de basculement à l’aide de [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Supprimez chaque base de données source à l’aide de [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) pour chacune des bases de données sur le serveur source. Les liens de géoréplication seront automatiquement rompus. 
1. Supprimez le serveur source à l’aide de [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Supprimez le coffre de clés, les conteneurs de stockage d’audit, le hub d’événements, l’instance AAD et les autres ressources dépendantes pour qu’elles ne vous soient plus facturées. 

## <a name="move-elastic-pools"></a>Déplacer les pools élastiques

### <a name="verify-prerequisites"></a>Vérifier la configuration requise 

1. Créez un serveur logique cible pour chaque serveur source. 
1. Configurez le pare-feu avec les exceptions appropriées à l’aide de [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Configurez les serveurs logiques avec les connexions appropriées. Si vous n’êtes pas administrateur de l’abonnement ou administrateur SQL Server, collaborez avec l’administrateur pour affecter les autorisations dont vous avez besoin. Pour plus d’informations, consultez [Guide pratique pour gérer la sécurité de la base de données Azure SQL après la reprise d’activité](sql-database-geo-replication-security-config.md). 
1. Si vos bases de données sont chiffrées avec TDE et que vous utilisez votre propre clé de chiffrement dans Azure Key Vault, vérifiez que le matériel de chiffrement correct est provisionné dans la région cible.
1. Créez un pool élastique cible pour chaque pool élastique source, en veillant à ce que le pool soit créé dans le même niveau de service, avec le même nom et la même taille. 
1. Si un audit de niveau base de données est activé, désactivez-le et activez plutôt l’audit au niveau du serveur. Après le basculement, l’audit au niveau de la base de données nécessitera le trafic inter-régions, qui n’est pas souhaitable ou possible après le déplacement. 
1. Pour les audits au niveau du serveur, vérifiez que :
    - Le conteneur de stockage, Log Analytics ou le hub d’événements avec les journaux d’audit existants est déplacé vers la région cible.
    - La configuration de l’audit est configurée sur le serveur cible. Pour plus d’informations, consultez [Audit de base de données SQL](sql-database-auditing.md).
1. Si votre instance a une stratégie de conservation à long terme (LTR), les sauvegardes LTR existantes resteront associées au serveur actuel. Le serveur cible étant différent, vous pourrez accéder aux sauvegardes LTR plus anciennes dans la région source à l’aide du serveur source, même si le serveur est supprimé. 

  > [!NOTE]
  > Cela ne sera pas suffisant pour les déplacements entre le cloud souverain et une région publique. Pour une telle migration, vous devrez déplacer les sauvegardes LTR vers le serveur cible, ce qui n’est pas pris en charge actuellement. 

### <a name="prepare-to-move"></a>Préparer le déplacement
 
1.  Créez un [groupe de basculement](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) distinct entre chaque pool élastique sur le serveur logique source et son pool élastique correspondant sur le serveur cible. 
1.  Ajoutez toutes les bases de données du pool au groupe de basculement. 
    - La réplication des bases de données ajoutées sera lancée automatiquement. Pour plus d’informations, consultez [Meilleures pratiques relatives à l’utilisation des groupes de basculement avec des bases de données uniques et des pools élastiques](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Bien qu’il soit possible de créer un groupe de basculement qui comprend plusieurs pools élastiques, nous vous recommandons vivement de créer un groupe de basculement distinct pour chaque pool. Si vous devez déplacer un grand nombre de bases de données dans plusieurs pools élastiques, vous pouvez exécuter les étapes de préparation en parallèle, puis lancer l’étape de déplacement en parallèle. Ce processus est mieux adapté et prend moins de temps que si vous avez plusieurs pools élastiques dans le même groupe de basculement. 

### <a name="monitor-the-preparation-process"></a>Superviser le processus de préparation

Vous pouvez appeler régulièrement [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) pour superviser la réplication de vos bases de données de la source vers la cible. L’objet de sortie de `Get-AzSqlDatabaseFailoverGroup` comprend une propriété pour **ReplicationState** : 
   - **ReplicationState = 2** (CATCH_UP) indique que la base de données est synchronisée et peut être basculée en toute sécurité. 
   - **ReplicationState = 0** (SEEDING) indique que la base de données n’est pas encore amorcée et qu’une tentative de basculement échouera. 

### <a name="test-synchronization"></a>Tester la synchronisation
 
Une fois que **ReplicationState** a la valeur `2`, connectez-vous à chaque base de données ou sous-ensemble de bases de données à l’aide du point de terminaison secondaire `<fog-name>.secondary.database.windows.net` et exécutez une requête sur les bases de données pour vérifier la connectivité, la configuration de sécurité et la réplication des données. 

### <a name="initiate-the-move"></a>Lancer le déplacement
 
1. Connectez-vous au serveur cible à l’aide du point de terminaison secondaire `<fog-name>.secondary.database.windows.net`.
1. Utilisez [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) pour faire de l’instance managée secondaire la base de données primaire avec synchronisation complète. Cette opération réussira ou sera annulée. 
1. Vérifiez que la commande s’est correctement exécutée en exécutant `nslook up <fog-name>.secondary.database.windows.net` pour vérifier que l’entrée CNAME DNS pointe vers l’adresse IP de la région cible. Si la commande Switch échoue, le CNAME ne sera pas mis à jour. 

### <a name="remove-the-source-elastic-pools"></a>Supprimer les pools élastiques sources
 
Une fois le déplacement terminé, supprimez les ressources dans la région source pour éviter les frais inutiles. 

1. Supprimez le groupe de basculement à l’aide de [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Supprimez chaque pool élastique source sur le serveur source à l’aide de [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Supprimez le serveur source à l’aide de [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Supprimez le coffre de clés, les conteneurs de stockage d’audit, le hub d’événements, l’instance AAD et les autres ressources dépendantes pour qu’elles ne vous soient plus facturées. 

## <a name="move-managed-instance"></a>Déplacer l’instance managée

### <a name="verify-prerequisites"></a>Vérifier la configuration requise
 
1. Pour chaque instance managée source, créez une instance managée cible de la même taille dans la région cible.  
1. Configurez le réseau pour une instance managée. Pour plus d’informations, consultez [Configuration réseau](sql-database-howto-managed-instance.md#network-configuration).
1. Configurez la base de données MASTER cible avec les connexions appropriées. Si vous n’êtes pas administrateur de l’abonnement ou administrateur SQL Server, collaborez avec l’administrateur pour affecter les autorisations dont vous avez besoin. 
1. Si vos bases de données sont chiffrées avec TDE et que vous utilisez votre propre clé de chiffrement dans Azure Key Vault, vérifiez que le coffre AKV avec des clés de chiffrement identiques existe dans les régions source et cible. Pour plus d’informations, consultez [Azure SQL Transparent Data Encryption avec des clés managées dans Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Si l’audit est activé pour l’instance, vérifiez que :
    - Le conteneur de stockage ou le hub d’événements avec les journaux existants est déplacé vers la région cible. 
    - L’audit est configuré sur l’instance cible. Pour plus d’informations, consultez [Audit avec une instance managée](sql-database-managed-instance-auditing.md).
1. Si votre instance a une stratégie de conservation à long terme (LTR), les sauvegardes LTR existantes resteront associées au serveur actuel. Le serveur cible étant différent, vous pourrez accéder aux sauvegardes LTR plus anciennes dans la région source à l’aide du serveur source, même si le serveur est supprimé. 

  > [!NOTE]
  > Cela ne sera pas suffisant pour les déplacements entre le cloud souverain et une région publique. Pour une telle migration, vous devrez déplacer les sauvegardes LTR vers le serveur cible, ce qui n’est pas pris en charge actuellement. 

### <a name="prepare-resources"></a>Préparer les ressources

Créez un groupe de basculement entre chaque instance source et l’instance cible correspondante.
    - La réplication de toutes les bases de données sur chaque instance sera lancée automatiquement. Pour plus d’informations, consultez [Groupes de basculement automatique](sql-database-auto-failover-group.md).

 
### <a name="monitor-the-preparation-process"></a>Superviser le processus de préparation

Vous pouvez appeler régulièrement [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) pour superviser la réplication de vos bases de données de la source vers la cible. L’objet de sortie de `Get-AzSqlDatabaseFailoverGroup` comprend une propriété pour **ReplicationState** : 
   - **ReplicationState = 2** (CATCH_UP) indique que la base de données est synchronisée et peut être basculée en toute sécurité. 
   - **ReplicationState = 0** (SEEDING) indique que la base de données n’est pas encore amorcée et qu’une tentative de basculement échouera. 

### <a name="test-synchronization"></a>Tester la synchronisation

Une fois que **ReplicationState** a la valeur `2`, connectez-vous à chaque base de données ou sous-ensemble de bases de données à l’aide du point de terminaison secondaire `<fog-name>.secondary.database.windows.net` et exécutez une requête sur les bases de données pour vérifier la connectivité, la configuration de sécurité et la réplication des données. 

### <a name="initiate-the-move"></a>Lancer le déplacement 

1. Connectez-vous au serveur cible à l’aide du point de terminaison secondaire `<fog-name>.secondary.database.windows.net`.
1. Utilisez [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) pour faire de l’instance managée secondaire la base de données primaire avec synchronisation complète. Cette opération réussira ou sera annulée. 
1. Vérifiez que la commande s’est correctement exécutée en exécutant `nslook up <fog-name>.secondary.database.windows.net` pour vérifier que l’entrée CNAME DNS pointe vers l’adresse IP de la région cible. Si la commande Switch échoue, le CNAME ne sera pas mis à jour. 

### <a name="remove-the-source-managed-instances"></a>Supprimer les instances managées sources
Une fois le déplacement terminé, supprimez les ressources dans la région source pour éviter les frais inutiles. 

1. Supprimez le groupe de basculement à l’aide de [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Cela supprimera la configuration du groupe de basculement et rompra les liens de géoréplication entre les deux instances. 
1. Supprimez l’instance managée source à l’aide de [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Supprimez toutes les ressources supplémentaires dans le groupe de ressources, telles que le cluster virtuel, le réseau virtuel et le groupe de sécurité. 

## <a name="next-steps"></a>Étapes suivantes 

[Gérez](sql-database-manage-after-migration.md) votre base de données Azure SQL Database une fois qu’elle a été migrée. 

