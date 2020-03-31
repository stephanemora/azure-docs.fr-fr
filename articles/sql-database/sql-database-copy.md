---
title: Copier une base de données
description: Créez une copie cohérente au niveau transactionnel d’une base de données Azure SQL Database existante sur le même serveur ou sur un autre serveur.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473742"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Copier une copie cohérente au niveau transactionnel d'une base de données Azure SQL Database

Azure SQL Database fournit plusieurs méthodes pour créer une copie cohérente au niveau transactionnel de la base de données Azure SQL existante ([base de données unique](sql-database-single-database.md)) sur le même serveur ou sur un autre serveur. Vous pouvez copier une base de données SQL à l’aide du portail Azure, de PowerShell ou de T-SQL.

## <a name="overview"></a>Vue d’ensemble

La copie de la base de données est une capture instantanée de la base de données source au moment de la demande de la copie. Vous pouvez sélectionner le même serveur ou un autre serveur. Vous pouvez également choisir de conserver son niveau de service et sa taille de calcul, ou d’utiliser une taille de calcul différente au sein du même niveau de service (édition). Une fois la copie terminée, elle devient une base de données indépendante et entièrement fonctionnelle. À ce stade, vous pouvez la mettre à niveau ou la rétrograder vers n’importe quelle édition. Les connexions, les utilisateurs et les autorisations peuvent être gérés indépendamment. La copie est créée à l’aide de la technologie de géoréplication. Une fois l’amorçage terminé, le lien de géoréplication est automatiquement arrêté. Toutes les exigences relatives à l’utilisation de la géoréplication s’appliquent à l’opération de copie de base de données. Pour plus d’informations, consultez [Présentation de la géoréplication active](sql-database-active-geo-replication.md).

> [!NOTE]
> Les [sauvegardes de base de données automatiques](sql-database-automated-backups.md) sont utilisées lorsque vous créez une copie de base de données.

## <a name="logins-in-the-database-copy"></a>Connexions dans la copie de la base de données

Lorsque vous copiez une base de données sur le même serveur SQL Database, les mêmes connexions peuvent être utilisées sur les deux bases de données. L'élément principal de sécurité que vous utilisez pour copier la base de données devient le propriétaire de la base de données sur la nouvelle base de données. 

Lorsque vous copiez une base de données vers un autre serveur SQL Database, le principal de sécurité qui a lancé l’opération de copie sur le serveur cible devient le propriétaire de la nouvelle base de données. 

Quel que soit le serveur cible, tous les utilisateurs de la base de données, leurs autorisations et leurs identificateurs de sécurité (SID) sont copiés vers la copie de base de données. Si vous utilisez des [utilisateurs de base de données autonome](sql-database-manage-logins.md) pour accéder aux données, vérifiez que la base de données copiée utilise les mêmes informations d’identification utilisateur afin de pouvoir y accéder immédiatement avec les mêmes informations d’identification, une fois la copie terminée.

Si vous utilisez des connexions au niveau du serveur pour l'accès aux données et copiez la base de données sur un autre serveur, l'accès par connexion peut ne pas fonctionner. Cela peut se produire si les connexions n'existent pas sur le serveur cible, ou si leurs mots de passe et identificateurs de sécurité (SID) sont différents. Pour en savoir plus sur la gestion des connexions lorsque vous copiez une base de données vers un autre serveur SQL Database, consultez la page [Gestion de la sécurité d’une base de données Azure SQL après la récupération d’urgence](sql-database-geo-replication-security-config.md). Une fois l'opération de copie sur un autre serveur réussie, et avant que d'autres utilisateurs ne soient redirigés, seule la connexion associée au propriétaire de la base de données, ou l'administrateur du serveur, peut se connecter à la base de données copiée. Pour résoudre les connexions et établir l’accès aux données à l’issue de l’opération de copie, consultez [Résoudre les connexions](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Copier une base de données à l’aide du portail Azure

Pour copier une base de données à l’aide du portail Azure, ouvrez la page de votre base de données, puis cliquez sur **Copier**.

   ![Copie de base de données](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>Copier une base de données à l’aide de PowerShell ou de l’interface de ligne de commande Azure

Pour copier une base de données, utilisez les exemples suivants.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour PowerShell, utilisez la cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy).

> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager (RM) est toujours pris en charge par Azure SQL Database, mais tous les développements à venir sont destinés au module Az.Sql. Le module AzureRM continue à recevoir des résolutions de bogues jusqu’à au moins décembre 2020.  Les arguments des commandes dans le module Az sont sensiblement identiques à ceux des modules AzureRm. Pour en savoir plus sur leur compatibilité, consultez [Présentation du nouveau module Az Azure PowerShell](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

La copie de la base de données est une opération asynchrone, mais la base de données cible est créée de suite après que la demande est acceptée. Si vous devez annuler une opération de copie en cours, supprimez la base de données cible à l’aide de l’applet de commande [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).

Pour un exemple complet de script PowerShell, consultez [Copier une base de données sur un nouveau serveur](scripts/sql-database-copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

La copie de la base de données est une opération asynchrone, mais la base de données cible est créée de suite après que la demande est acceptée. Si vous devez annuler une opération de copie en cours, déposez la base de données cible à l’aide de la commande [az sql db delete](/cli/azure/sql/db#az-sql-db-delete).

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>Rôles RBAC pour gérer la copie de la base de données

Pour créer une copie de base de données, vous devez disposer des rôles suivants :

- propriétaire de l’abonnement ou
- Rôle Contributeur de SQL Server
- Rôle personnalisé sur les bases de données source et cible avec l’autorisation suivante :

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

Pour annuler une copie de base de données, vous devez disposer des rôles suivants :

- propriétaire de l’abonnement ou
- Rôle Contributeur de SQL Server
- Rôle personnalisé sur les bases de données source et cible avec l’autorisation suivante :

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

Pour gérer la copie de base de données à l’aide du portail Azure, vous devez également disposer des autorisations suivantes :

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Si vous voulez voir les opérations sous « deployments » dans le groupe de ressources sur le portail (opérations entre plusieurs fournisseurs de ressources, dont les opérations SQL), vous devez disposer des rôles RBAC supplémentaires suivants :

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Copier une base de données à l’aide de Transact-SQL

Connectez-vous à la base de données master à l’aide de la connexion de l’administrateur du serveur ou la connexion qui a créé la base de données que vous souhaitez copier. Pour que la copie de la base de données réussisse, les connexions autres que celle de l’administrateur du serveur doivent être membres du rôle `dbmanager`. Pour plus d’informations sur les connexions et la connexion au serveur, consultez la page [Gérer les connexions](sql-database-manage-logins.md).

Démarrez la copie de la base de données source avec l’instruction [CREATE DATABASE ... AS COPY OF](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database). L’instruction T-SQL continue de fonctionner jusqu'à ce que l'opération de copie de la base de données soit terminée.

> [!NOTE]
> L’arrêt de l’instruction T-SQL ne met pas fin à l'opération de copie de la base de données. Pour mettre fin à l'opération, supprimez la base de données cible.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>Copie d'une base de données SQL sur le même serveur

Connectez-vous à la base de données master à l’aide de la connexion de l’administrateur du serveur ou la connexion qui a créé la base de données que vous souhaitez copier. Pour que la copie de la base de données réussisse, les connexions autres que celle de l’administrateur du serveur doivent être membres du rôle `dbmanager`.

Cette commande copie Database1 dans une nouvelle base de données nommée Database2 sur le même serveur. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Copie d'une base de données SQL vers un autre serveur

Connectez-vous à la base de données master du serveur cible sur lequel la nouvelle base de données doit être créée. Utilisez une connexion avec les mêmes nom et mot de passe que le propriétaire de la base de données de la base de données source sur le serveur source. La connexion sur le serveur cible doit également être membre du rôle `dbmanager` ou être la connexion de l’administrateur du serveur.

Cette commande copie Database1 sur server1 dans une nouvelle base de données nommée Database2 sur server2. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Les pare-feu des deux serveurs doivent être configurés pour autoriser la connexion entrante à partir de l’adresse IP du client émettant la commande T-SQL CREATE DATABASE ... AS COPY OF.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copier une base de données SQL vers un autre abonnement

Vous pouvez utiliser les étapes décrites dans la section [Copier une base de données SQL vers un autre serveur](#copy-a-sql-database-to-a-different-server) pour copier votre base de données sur un serveur SQL Database d’un autre abonnement à l’aide de l’instruction T-SQL. Veillez à utiliser une connexion avec les mêmes nom et mot de passe que le propriétaire de la base de données source. En outre, la connexion doit être un membre du rôle `dbmanager` ou un administrateur de serveur, à la fois sur les serveurs source et cible.

> [!NOTE]
> Le [portail Azure](https://portal.azure.com), PowerShell et l’interface de ligne de commande Azure ne prennent pas en charge la copie de la base de données vers un autre abonnement.

### <a name="monitor-the-progress-of-the-copying-operation"></a>Contrôle de la progression de l’opération de copie

Contrôlez le processus de copie en interrogeant les vues [sys.databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database) et [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). Pendant que la copie est en cours, la colonne **state_desc** de la vue sys.databases pour la nouvelle base de données est définie sur **COPYING**.

* Si la copie échoue, la colonne **state_desc** de la vue sys.databases pour la nouvelle base de données est définie sur **SUSPECT**. Exécutez l'instruction DROP sur la nouvelle base de données et réessayez ultérieurement.
* Si la copie réussit, la colonne **state_desc** de la vue sys.databases pour la nouvelle base de données est définie sur **ONLINE**. La copie est terminée et la nouvelle base de données est une base de données normale, qui peut être modifiée indépendamment de la base de données source.

> [!NOTE]
> Si vous décidez d’annuler la copie pendant qu’elle est en cours, exécutez l’instruction [DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) sur la nouvelle base de données.

> [!IMPORTANT]
> Si vous devez créer une copie avec un objectif de service beaucoup plus petit que la source, la base de données cible peut ne pas disposer de ressources suffisantes pour terminer le processus d’amorçage et peut entraîner l’échec de l’opération de copie. Dans ce scénario, utilisez une requête de géorestauration pour créer une copie sur un autre serveur et/ou dans une autre région. Pour plus d’informations, consultez [Récupérer une base de données Azure SQL à l’aide des sauvegardes automatisées d’une base de données](sql-database-recovery-using-backups.md#geo-restore).

## <a name="resolve-logins"></a>Résolution des connexions

Une fois que la nouvelle base de données est en ligne sur le serveur cible, utilisez l'instruction [ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) pour remapper les utilisateurs de la nouvelle base de données avec des connexions sur le serveur cible. Pour résoudre les problèmes d’utilisateurs orphelins, consultez [Dépannage des utilisateurs orphelins](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Vous pouvez également consulter [Gestion de la sécurité de la base de données Azure SQL après une reprise d’activité](sql-database-geo-replication-security-config.md).

Tous les utilisateurs de la nouvelle base de données conservent les autorisations qu’ils avaient dans la base de données source. L'utilisateur qui a initié la copie de la base de données devient le propriétaire de celle-ci. Une fois la copie réussie et avant que les autres utilisateurs ne soient remappés, seul le propriétaire de la base de données peut se connecter à la nouvelle base de données.

Pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données sur un autre serveur SQL Database, consultez [Configurer et gérer la sécurité Azure SQL Database pour la géo-restauration ou le basculement](sql-database-geo-replication-security-config.md).

## <a name="database-copy-errors"></a>Erreurs de copie de base de données

Les erreurs suivantes peuvent survenir lors de la copie d’une base de données dans Azure SQL Database. Pour en savoir plus, consultez [Copie d’une base de données Azure SQL](sql-database-copy.md).

| Code d'erreur | severity | Description |
| ---:| ---:|:--- |
| 40635 |16 |Le client avec l'adresse IP’%.&#x2a;ls’ est temporairement désactivé. |
| 40637 |16 |La copie de base de données est actuellement désactivée. |
| 40561 |16 |La copie de base de données a échoué. La base de données source ou cible n'existe pas. |
| 40562 |16 |La copie de base de données a échoué. La base de données source a été supprimée. |
| 40563 |16 |La copie de base de données a échoué. La base de données cible a été supprimée. |
| 40564 |16 |La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez. |
| 40565 |16 |La copie de base de données a échoué. Pas plus d'une copie de base de données simultanée de la même source est autorisée. Supprimez la base de données cible et réessayez ultérieurement. |
| 40566 |16 |La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez. |
| 40567 |16 |La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez. |
| 40568 |16 |La copie de base de données a échoué. La base de données source n'est plus disponible. Supprimez la base de données cible et réessayez. |
| 40569 |16 |La copie de base de données a échoué. La base de données cible n'est plus disponible. Supprimez la base de données cible et réessayez. |
| 40570 |16 |La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez ultérieurement. |
| 40571 |16 |La copie de base de données a échoué en raison d'une erreur interne. Supprimez la base de données cible et réessayez ultérieurement. |

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les connexions, consultez [Gérer les connexions](sql-database-manage-logins.md) et [Comment gérer la sécurité d’une base de données Azure SQL après une reprise d’activité](sql-database-geo-replication-security-config.md).
- Pour exporter une base de données, consultez [Exporter la base de données vers un fichier BACPAC](sql-database-export.md).
