---
title: Configurer la sécurité des réplicas nommés pour autoriser l’accès isolé
description: Découvrez les considérations de sécurité pour la configuration et la gestion du réplica nommé afin qu’un utilisateur puisse accéder au réplica nommé, mais pas aux autres réplicas.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
author: yorek
ms.author: damauri
ms.reviewer: ''
ms.date: 7/27/2021
ms.openlocfilehash: 88410573dbefdfdedb4628babfffc601b19bed36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562990"
---
# <a name="configure-isolated-access-to-a-hyperscale-named-replica"></a>Configurer un accès isolé à un réplica hyperscale nommé
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Cet article décrit la procédure permettant d’accorder l’accès à un réplica Azure SQL Hyperscale [nommé](service-tier-hyperscale-replicas.md) sans accorder l’accès au réplica principal ou à d’autres réplicas nommés. Ce scénario autorise l’isolation des ressources et de la sécurité d’un réplica nommé, car le réplica nommé s’exécutera à l’aide de son propre nœud de calcul. il est utile chaque fois qu’un accès en lecture seule isolé à une base de données Azure SQL Hyperscale est nécessaire. Isolé, dans ce contexte, signifie que le processeur et la mémoire ne sont pas partagés entre le réplica principal et le réplica nommé. les requêtes qui s’exécutent sur le réplica nommé n’utilisent pas les ressources de calcul du réplica principal ou des autres réplicas, et les principaux qui accèdent au réplica nommé ne peuvent pas accéder à d’autres réplicas, y compris le principal.

## <a name="create-a-login-in-the-master-database-on-the-primary-server"></a>Créer une connexion dans la base de données Master sur le serveur principal

Dans la base de données `master` sur le serveur logique hébergeant la base de données *principale*, exécutez la commande suivante pour créer une nouvelle connexion. Utilisez votre propre mot de passe fort et unique.

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!';
```

Récupérez la valeur hexadécimale SID pour la connexion créée à partir de la vue système `sys.sql_logins` :

```sql
select sid from sys.sql_logins where name = 'third-party-login';
```

Désactivez la connexion. Cela empêchera cette connexion d’accéder à une base de données sur le serveur qui héberge le réplica principal.

```sql
alter login [third-party-login] disable;
```

## <a name="create-a-user-in-the-primary-read-write-database"></a>Créer un utilisateur dans la base de données principale en lecture-écriture

Une fois la connexion créée, connectez-vous au réplica principal en lecture-écriture de votre base de données, par exemple WideWorldImporters (vous pouvez trouver un exemple de script pour le restaurer ici : [restaurer la base de données dans Azure SQL](https://github.com/yorek/azure-sql-db-samples/tree/master/samples/01-restore-database)) et créer un utilisateur de base de données pour cette connexion :

```sql
create user [third-party-user] from login [third-party-login];
```

En guise d’étape facultative, une fois que l’utilisateur de base de données a été créé, vous pouvez supprimer la connexion au serveur créée à l’étape précédente si la connexion est réactivée de quelque manière que ce soit. Connectez-vous à la base de données master sur le serveur logique qui héberge la base de données primaire, puis exécutez la commande suivante :

```sql
drop login [third-party-login];
```

## <a name="create-a-named-replica-on-a-different-logical-server"></a>Créer un réplica nommé sur un serveur logique différent

Créez un serveur logique Azure SQL qui sera utilisé pour isoler l’accès au réplica nommé. Suivez les instructions disponibles dans [Créer et gérer des serveurs et des bases de données uniques dans Azure SQL Database](single-database-manage.md). Pour créer un réplica nommé, ce serveur doit se trouver dans la même région Azure que le serveur hébergeant le réplica principal.

À l’aide de, par exemple, AZ CLI :

```azurecli
az sql server create -g MyResourceGroup -n MyNamedReplicaServer -l MyLocation --admin-user MyAdminUser --admin-password MyStrongADM1NPassw0rd!
```

Créez ensuite un réplica nommé pour la base de données primaire sur ce serveur. Par exemple, à l’aide d’AZ CLI :

```azurecli
az sql db replica create -g MyResourceGroup -n WideWorldImporters -s MyPrimaryServer --secondary-type Named --partner-database WideWorldImporters_NR --partner-server MyNamedReplicaServer
```

## <a name="create-a-login-in-the-master-database-on-the-named-replica-server"></a>Créer une connexion dans la base de données Master sur le serveur de replication principal

Connectez-vous à la base de données `master` sur le serveur logique hébergeant le réplica nommé créé à l’étape précédente. Ajoutez la connexion à l’aide du SID récupéré à partir du réplica principal :

```sql
create login [third-party-login] with password = 'Just4STRONG_PAZzW0rd!', sid = 0x0...1234;
```

À ce stade, les utilisateurs et les applications qui utilisent `third-party-login` peuvent se connecter au réplica nommé, mais pas au réplica principal.

## <a name="grant-object-level-permissions-within-the-database"></a>Accorder des autorisations au niveau objet dans la base de données

Une fois que vous avez configuré l’authentification de connexion comme décrit, vous pouvez utiliser les instructions régulières `GRANT`, `DENY` et `REVOKE` pour gérer l’autorisation, ou des autorisations au niveau objet au sein de la base de données. Dans ces instructions, référencez le nom de l’utilisateur que vous avez créé dans la base de données ou un rôle de base de données qui comprend cet utilisateur en tant que membre. N’oubliez pas d’exécuter ces commandes sur le réplica principal. Les modifications sont propagées à tous les réplicas secondaires, mais elles ne sont effectives que sur le réplica nommé dans lequel la connexion au niveau du serveur a été créée.

N’oubliez pas que, par défaut, un utilisateur nouvellement créé possède un ensemble minimal d’autorisations accordé (par exemple, il ne peut pas accéder aux tables utilisateur). Si vous souhaitez autoriser `third-party-user` à lire des données dans une table, vous devez explicitement accorder l'autorisation `SELECT` :

```sql
grant select on [Application].[Cities] to [third-party-user];
```

Au lieu d'accorder des autorisations individuellement sur chaque table, vous pouvez ajouter l'utilisateur au [rôle de base de données](/sql/relational-databases/security/authentication-access/database-level-roles) `db_datareaders` pour autoriser l'accès en lecture à toutes les tables, ou vous pouvez utiliser des [schémas](/sql/relational-databases/security/authentication-access/create-a-database-schema) pour [autoriser l'accès](/sql/t-sql/statements/grant-schema-permissions-transact-sql) à toutes les tables existantes et nouvelles dans un schéma.

## <a name="test-access"></a>Tester l’accès

Vous pouvez tester cette configuration à l’aide de n’importe quel outil client et tenter de vous connecter au réplica principal et au réplica nommé. Par exemple, en utilisant `sqlcmd`, vous pouvez essayer de vous connecter au réplica principal à l'aide de l'utilisateur `third-party-login` :

```
sqlcmd -S MyPrimaryServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters
```

Cela génère une erreur, car l’utilisateur n’est pas autorisé à se connecter au serveur :

```
Sqlcmd: Error: Microsoft ODBC Driver 13 for SQL Server : Login failed for user 'third-party-login'. Reason: The account is disabled.
```

La tentative de connexion au réplica nommé a échoué :

```
sqlcmd -S MyNamedReplicaServer.database.windows.net -U third-party-login -P Just4STRONG_PAZzW0rd! -d WideWorldImporters_NR
```

Aucune erreur n’est retournée, et les requêtes peuvent être exécutées sur le réplica nommé comme autorisé par les autorisations de niveau objet accordées.

Pour plus d’informations :

* Serveurs logiques Azure SQL, consultez [Qu’est-ce qu’un serveur dans Azure SQL Database](logical-servers.md)
* Gestion de l’accès aux bases de données et des identifiants de connexion, consultez [Sécurité SQL Database : gérer la sécurité d’accès et de connexion aux bases de données](logins-create-manage.md)
* Autorisations de moteur de base de données, consultez [Autorisations](/sql/relational-databases/security/permissions-database-engine) 
* Accorder des autorisations d'objet, voir [ACCORDER des autorisations d'objet](/sql/t-sql/statements/grant-object-permissions-transact-sql)



