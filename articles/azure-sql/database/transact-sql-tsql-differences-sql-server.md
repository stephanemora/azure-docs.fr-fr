---
title: Résoudre les différences T-SQL – Migration
description: Instructions T-SQL qui ne sont pas entièrement prises en charge dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: mokabiru
ms.author: mokabiru
ms.reviewer: mathoma
ms.date: 06/17/2021
ms.openlocfilehash: 479800be0301d40fb1f20127f3d35fd11e7ba69e
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113004589"
---
# <a name="t-sql-differences-between-sql-server-and-azure-sql-database"></a>Différences de T-SQL entre SQL Server et Azure SQL Database

Au moment de [migrer votre base de données](migrate-to-database-from-sql-server.md) de SQL Server vers Azure SQL Database, vous découvrirez peut-être que la conception des bases de données SQL Server est à revoir avant de pouvoir effectuer la migration. Cet article comprend des informations vous permettant d’apporter les révisions nécessaires et de comprendre les raisons sous-jacentes à ces changements. Pour détecter les incompatibilités et migrer les bases de données vers Azure SQL Database, utilisez l’[Assistant Migration de données](/sql/dma/dma-overview) (DMA, Data Migration Assistant).

## <a name="overview"></a>Vue d'ensemble

La plupart des fonctionnalités T-SQL utilisées par les applications sont entièrement prises en charge dans Microsoft SQL Server et Azure SQL Database. Par exemple, les principaux composants SQL tels que les types de données, les opérateurs ainsi que les fonctions de chaîne, arithmétiques, logiques et de curseur fonctionnent de la même façon dans SQL Server et SQL Database. Il existe toutefois quelques différences au niveau des éléments du langage de définition de données (DDL) et des éléments du langage de manipulation de données (DML). Ceux-ci génèrent en effet des instructions et des requêtes T-SQL qui ne sont que partiellement prises en charge (nous y reviendrons plus loin dans cet article).

Par ailleurs, Azure SQL Database étant conçu pour isoler les fonctionnalités des dépendances sur les bases de données système et le système d’exploitation, certaines fonctionnalités et syntaxes ne sont pas du tout prises en charge. La plupart des fonctionnalités au niveau de l’instance ne sont donc pas prises en charge dans SQL Database. Les instructions et les options T-SQL ne sont pas disponibles si elles configurent des composants du système d’exploitation et des options au niveau de l’instance ou si elles spécifient la configuration du système de fichiers. Si de telles fonctionnalités sont nécessaires, vous trouverez la plupart du temps une alternative appropriée en procédant différemment à partir de SQL Database ou d’un autre service/d’une autre fonctionnalité Azure.

Par exemple, la haute disponibilité est intégrée à Azure SQL Database. Les instructions T-SQL relatives aux groupes de disponibilité ne sont pas prises en charge par SQL Database, de même que les vues de gestion dynamique associées aux groupes de disponibilité Always On.

Pour obtenir la liste des fonctionnalités prises en charge et non prises en charge par SQL Database, consultez [Comparaison des fonctionnalités d’Azure SQL Database](features-comparison.md). Cette page complète cet article. Elle est consacrée aux instructions T-SQL.

## <a name="t-sql-syntax-statements-with-partial-differences"></a>Instructions T-SQL avec des différences partielles

Les instructions DDL de base sont disponibles, mais les extensions d’instruction DDL liées aux fonctionnalités non prises en charge telles que le placement des fichiers sur le disque ne sont pas prises en charge.

- Dans SQL Server, les instructions `CREATE DATABASE` et `ALTER DATABASE` ont plus d’une trentaine d’options. Les instructions incluent des options liées à l’emplacement des fichiers, à FILESTREAM et à Service Broker qui s’appliquent uniquement à SQL Server. Ceci peut être sans importance si vous créez des bases de données dans SQL Database avant la migration. Cependant, si vous migrez du code T-SQL qui crée des bases de données, vous devez comparer [CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) à la syntaxe SQL Server décrite dans [CREATE DATABASE (SQL Server T-SQL)](/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver15&preserve-view=true) pour vérifier que toutes les options que vous utilisez sont prises en charge. `CREATE DATABASE` pour Azure SQL Database compte également des options d’objectif de service et de pool élastique qui s’appliquent uniquement à SQL Database.
- Les instructions `CREATE TABLE` et `ALTER TABLE` offrent les options `FILETABLE` et `FILESTREAM` que vous ne pouvez pas utiliser sur SQL Database, car elles ne sont pas prises en charge.
- Les instructions `CREATE LOGIN` et `ALTER LOGIN` sont prises en charge, mais elles n’offrent pas toutes les options disponibles dans SQL Server. Pour accroître la portabilité de votre base de données, SQL Database recommande, dans la mesure du possible, de remplacer les connexions par des utilisateurs de base de données autonome. Pour plus d’informations, consultez [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true), [ALTER LOGIN](/sql/t-sql/statements/alter-login-transact-sql?view=azuresqldb-current&preserve-view=true) et [Gérer les connexions et utilisateurs](logins-create-manage.md).

## <a name="t-sql-syntax-not-supported-in-azure-sql-database"></a>Syntaxe T-SQL non prise en charge dans Azure SQL Database

En plus des instructions T-SQL liées aux fonctionnalités non prises en charge décrites dans [Comparaison des fonctionnalités Azure SQL Database](features-comparison.md), les instructions et groupes d’instructions suivants ne sont pas pris en charge. Si la base de données à migrer utilise les fonctionnalités et instructions T-SQL suivantes, vous devez donc remanier votre application pour les éliminer.

- Classement des objets système.
- Relatif à la connexion : instructions relatives aux points de terminaison. SQL Database ne prend pas en charge l’authentification Windows, mais prend en charge l’authentification Azure Active Directory. Ceci comprend l’authentification des principaux Active Directory fédérés avec Azure Active Directory. Pour plus d’informations, consultez [Connexion à SQL Database ou Azure Azure Synapse Analytics à l’aide de l’authentification Azure Active Directory](authentication-aad-overview.md).
- Requêtes sur plusieurs bases de données et plusieurs instances à l’aide de noms en trois ou quatre parties. Les noms en trois parties référençant la base de données `tempdb` et la base de données actuelle sont pris en charge. La [requête élastique](elastic-query-overview.md) prend en charge les références en lecture seule aux tables d’autres bases de données MSSQL.
- Chaînage des propriétés des bases de données croisées et la propriété de base de données `TRUSTWORTHY`.
- `EXECUTE AS LOGIN`. Utilisez `EXECUTE AS USER` à la place.
- Gestion de clés extensible pour les clés de chiffrement. Les [clés gérées par le client](transparent-data-encryption-byok-overview.md) TDE (Transparent Data Encryption) et les [clés principales de colonne](always-encrypted-azure-key-vault-configure.md) Always Encrypted peuvent être stockées dans Azure Key Vault.
- Gestion des événements : notifications d’événements, notifications de requête.
- Propriétés de fichier : syntaxe associée au nom, au placement et à la taille des fichiers de base de données ainsi qu’à d’autres propriétés de fichier gérées automatiquement par SQL Database.
- Haute disponibilité : syntaxe liée à la haute disponibilité et à la récupération de base de données, qui sont gérées par SQL Database. Ceci inclut la syntaxe pour la sauvegarde, la restauration, la fonctionnalité Always On, la mise en miroir de bases de données, la copie des journaux de transaction et les modèles de récupération.
- Syntaxe liée à la réplication de capture instantanée, transactionnelle et de fusion, qui n’est pas disponible dans SQL Database. Les [abonnements de réplication](replication-to-sql-database.md) sont pris en charge.
- Fonctions : `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`.
- Configuration de l’instance : syntaxe liée à la mémoire, aux threads de travail, à l’affinité du processeur et aux indicateurs de trace du serveur. Utilisez plutôt des niveaux de service et des tailles de calcul.
- `KILL STATS JOB`.
- `OPENQUERY`, `OPENDATASOURCE` et noms en quatre parties.
- .NET Framework : intégration du CLR
- Recherche sémantique
- Informations d’identification du serveur : utilisez les [informations d’identification de la base de données](/sql/t-sql/statements/create-database-scoped-credential-transact-SQL) à la place.
- Autorisations de niveau serveur : les autorisations `GRANT`, `REVOKE` et `DENY` de niveau serveur ne sont pas prises en charge. Certaines autorisations de niveau serveur sont remplacées par des autorisations de niveau base de données ou accordées implicitement par des rôles serveur intégrés. Pour certaines vues de gestion dynamique et vues de catalogue de niveau serveur, des vues de niveau base de données similaires sont disponibles.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Voir `sp_configure` et `RECONFIGURE`. [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) est pris en charge.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent : Syntaxe reposant sur SQL Server Agent ou la base de données MSDB : alertes, opérateurs, serveurs d’administration centrale. Utilisez des scripts tels que des scripts PowerShell à la place.
- Audit SQL Server : utilisez plutôt l’[audit](auditing-overview.md) SQL Database.
- Trace SQL Server.
- Indicateurs de trace.
- Débogage T-SQL.
- Déclencheurs avec étendue au niveau serveur ou déclencheurs d’ouverture de session.
- Instruction `USE` : pour basculer le contexte de base de données vers une autre base de données, vous devez créer une connexion à cette base de données.

## <a name="full-t-sql-reference"></a>Référence T-SQL complète

Pour plus d’informations sur la grammaire et l’utilisation de T-SQL et pour obtenir des exemples, consultez [Référence T-SQL (moteur de base de données)](/sql/t-sql/language-reference).

### <a name="about-the-applies-to-tags"></a>À propos des balises « S’applique à »

La référence T-SQL comprend des articles relatifs à toutes les versions récentes de SQL Server. Sous le titre de l’article se trouve une barre d’icônes qui liste les plateformes MSSQL et indique l’applicabilité. Par exemple, la fonction des groupes de disponibilité ont été introduits dans SQL Server 2012. L’article [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql) indique que l’instruction s’applique à **SQL Server (à partir de 2012)** . Cette instruction ne s’applique pas à SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure Synapse Analytics ni Parallel Data Warehouse.

Dans certains cas, le sujet général d’un article peut être utilisé dans un produit, mais il existe des différences mineures entre les produits. Les différences sont indiquées dans l’article comme il convient. Par exemple, l’article `CREATE TRIGGER` est disponible dans SQL Database. Cependant, l’option `ALL SERVER` pour les déclencheurs de niveau serveur indique que ces derniers ne peuvent pas être utilisés dans SQL Database. Utilisez plutôt des déclencheurs de niveau base de données.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des fonctionnalités prises en charge et non prises en charge par SQL Database, consultez [Comparaison des fonctionnalités d’Azure SQL Database](features-comparison.md).

Pour détecter les problèmes de compatibilité dans vos bases de données SQL Server avant une migration vers Azure SQL Database et pour migrer vos bases de données, utilisez l’[Assistant Migration de données](/sql/dma/dma-overview).