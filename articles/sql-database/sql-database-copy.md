---
title: Copie d’une base de données SQL Azure | Documents Microsoft
description: Créez une copie cohérente au niveau transactionnel d’une base de données Azure SQL Database existante sur le même serveur ou sur un autre serveur.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 2aeb756bda50597bf3e43c0c84391e0750bd8acb
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486816"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Copier une copie cohérente au niveau transactionnel d'une base de données Azure SQL Database

Azure SQL Database fournit plusieurs méthodes pour créer une copie cohérente au niveau transactionnel de la base de données SQL Azure existante sur le même serveur ou sur un autre serveur. Vous pouvez copier une base de données SQL à l’aide du portail Azure, de PowerShell ou de T-SQL. 

## <a name="overview"></a>Présentation

La copie de la base de données est une capture instantanée de la base de données source au moment de la demande de la copie. Vous pouvez sélectionner le même serveur ou un autre serveur, son niveau de service et sa taille de calcul, ou une taille de calcul différente au sein du même niveau de service (édition). Une fois la copie terminée, elle devient une base de données indépendante et entièrement fonctionnelle. À ce stade, vous pouvez la mettre à niveau ou la rétrograder vers n’importe quelle édition. Les connexions, les utilisateurs et les autorisations peuvent être gérés indépendamment.  

> [!NOTE]
> Les [sauvegardes de base de données automatiques](sql-database-automated-backups.md) sont utilisées lorsque vous créez une copie de base de données.

## <a name="logins-in-the-database-copy"></a>Connexions dans la copie de la base de données

Lorsque vous copiez une base de données sur le même serveur SQL Database, les mêmes connexions peuvent être utilisées sur les deux bases de données. L'élément principal de sécurité que vous utilisez pour copier la base de données devient le propriétaire de la base de données sur la nouvelle base de données. Tous les utilisateurs de la base de données, leurs autorisations et leurs identificateurs de sécurité (SID) sont copiés vers la copie de base de données.  

Lorsque vous copiez une base de données vers un autre serveur SQL Database, le principal de sécurité sur le nouveau serveur devient le propriétaire de la nouvelle base de données. Si vous utilisez des [utilisateurs de base de données autonome](sql-database-manage-logins.md) pour accéder aux données, vérifiez que les bases de données primaire et secondaire ont toujours les mêmes informations d’identification utilisateur afin de pouvoir y accéder immédiatement avec les mêmes informations d’identification, une fois la copie terminée. 

Si vous utilisez [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), vous n’avez plus du tout besoin de gérer les informations d’identification dans la copie. Toutefois, lorsque vous copiez la base de données sur un nouveau serveur, l’accès par connexion peut ne pas fonctionner, car ces connexions n’existent pas sur le nouveau serveur. Pour en savoir plus sur la gestion des connexions lorsque vous copiez une base de données vers un autre serveur SQL Database, consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md). 

Une fois la copie réussie et avant que les autres utilisateurs ne soient remappés, seule la connexion qui a initié la copie, le propriétaire de la base de données, peut se connecter à la nouvelle base de données. Pour résoudre les connexions à l’issue de l’opération de copie, consultez [Résoudre les connexions](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Copier une base de données à l’aide du portail Azure

Pour copier une base de données à l’aide du portail Azure, ouvrez la page de votre base de données, puis cliquez sur **Copier**. 

   ![Copie de base de données](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Copier une base de données à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont pour le module Az.Sql. Pour ces applets de commande, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments pour les commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

Pour copier une base de données à l’aide de PowerShell, utilisez le [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) applet de commande. 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Pour un exemple complet de script, consultez [Copier une base de données sur un nouveau serveur](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Copier une base de données à l’aide de Transact-SQL

Connectez-vous à la base de données maître à l’aide de la connexion principale niveau serveur ou la connexion qui a créé la base de données que vous souhaitez copier. Pour que la copie de la base de données réussisse, les connexions autres que la connexion principale niveau serveur doivent être membres du rôle dbmanager. Pour plus d’informations sur les connexions et la connexion au serveur, consultez la page [Gérer les connexions](sql-database-manage-logins.md).

Démarrez la copie de la base de données source avec l’instruction [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . L'exécution de cette instruction initie le processus de copie de la base de données. Étant donné que la copie d'une base de données est un processus asynchrone, l'instruction CREATE DATABASE est retournée avant la fin de la copie de la base de données.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copie d'une base de données SQL sur le même serveur

Connectez-vous à la base de données maître à l’aide de la connexion principale niveau serveur ou la connexion qui a créé la base de données que vous souhaitez copier. Pour que la copie de la base de données réussisse, les connexions autres que la connexion principale niveau serveur doivent être membres du rôle dbmanager.

Cette commande copie Database1 dans une nouvelle base de données nommée Database2 sur le même serveur. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copie d'une base de données SQL vers un autre serveur

Connectez-vous à la base de données MASTER du serveur de destination, le serveur SQL Database sur lequel la nouvelle base de données doit être créée. Utilisez une connexion pourvue des mêmes nom et mot de passe que le propriétaire de la base de données source sur le serveur SQL Database source. La connexion sur le serveur de destination doit également être membre du rôle dbmanager ou être la connexion principale niveau serveur.

Cette commande copie Database1 sur server1 dans une nouvelle base de données nommée Database2 sur server2. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;

## <a name="to-move-a-database-between-subscriptions"></a>Pour déplacer une base de données entre des abonnements

Dans le [portail Azure](https://portal.azure.com), cliquez sur **Serveurs SQL**, puis sélectionnez le serveur qui héberge votre base de données dans la liste. Cliquez sur **Déplacer**, puis sélectionnez les ressources à déplacer et l’abonnement cible.

### <a name="monitor-the-progress-of-the-copying-operation"></a>Contrôle de la progression de l’opération de copie

Contrôlez le processus de copie en interrogeant les vues sys.databases et sys.dm_database_copies. Pendant que la copie est en cours, la colonne **state_desc** de la vue sys.databases pour la nouvelle base de données est définie sur **COPYING**.

* Si la copie échoue, la colonne **state_desc** de la vue sys.databases pour la nouvelle base de données est définie sur **SUSPECT**. Exécutez l'instruction DROP sur la nouvelle base de données et réessayez ultérieurement.
* Si la copie réussit, la colonne **state_desc** de la vue sys.databases pour la nouvelle base de données est définie sur **ONLINE**. La copie est terminée et la nouvelle base de données est une base de données normale, qui peut être modifiée indépendamment de la base de données source.

> [!NOTE]
> Si vous décidez d’annuler la copie pendant qu’elle est en cours, exécutez l’instruction [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) sur la nouvelle base de données. Vous pouvez également exécuter l'instruction DROP DATABASE sur la base de données source pour annuler le processus de copie.

## <a name="resolve-logins"></a>Résolution des connexions

Une fois que la nouvelle base de données est en ligne sur le serveur de destination, utilisez l'instruction [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) pour remapper les utilisateurs de la nouvelle base de données avec des connexions sur le serveur de destination. Pour résoudre les problèmes d’utilisateurs orphelins, consultez [Dépannage des utilisateurs orphelins](https://msdn.microsoft.com/library/ms175475.aspx). Vous pouvez également consulter [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).

Tous les utilisateurs de la nouvelle base de données conservent les autorisations qu’ils avaient dans la base de données source. L'utilisateur qui a initié la copie de la base de données devient le propriétaire de celle-ci et reçoit un nouvel identificateur de sécurité (SID). Une fois la copie réussie et avant que les autres utilisateurs ne soient remappés, seule la connexion qui a initié la copie, le propriétaire de la base de données, peut se connecter à la nouvelle base de données.

Pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données sur un autre serveur SQL Database, consultez [Configurer et gérer la sécurité Azure SQL Database pour la géo-restauration ou le basculement](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les connexions, consultez [Gérer les connexions](sql-database-manage-logins.md) et [Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md).
* Pour exporter une base de données, consultez [Exporter la base de données vers un fichier BACPAC](sql-database-export.md).
