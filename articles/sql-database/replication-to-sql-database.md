---
title: Réplication dans Azure SQL Database | Microsoft Docs
description: Découvrez comment utiliser la réplication SQL Server avec les bases de données uniques Azure SQL Database et les bases de données des pools élastiques
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b9d6569504b5352c6187afe12d903c986019c517
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646800"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Réplication des bases de données uniques et mises en pool SQL Database

La réplication SQL Server peut être configurée pour les bases de données uniques et mises en pool d’un [serveur SQL Database](sql-database-servers.md) dans Azure SQL Database.  

## <a name="supported-configurations"></a>**Configurations prises en charge :**
  
- Le serveur SQL Server peut être une instance SQL Server exécutée localement ou une instance SQL Server exécutée sur une machine virtuelle Azure dans le cloud. Pour plus d’informations, consultez [Présentation de SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- La base de données SQL Azure doit être abonnée à un abonnement par émission de données sur un serveur de publication SQL Server.  
- La base de données de distribution et les agents de réplication ne peuvent pas être placés dans une base de données SQL Azure.  
- Les réplications par capture instantanée et par transaction monodirectionnelle sont prises en charge. Les réplications transactionnelles pair à pair et les réplications de fusion ne sont pas prises en charge.
- La réplication est disponible en préversion publique dans Azure SQL Database Managed Instance. Managed Instance peut héberger des bases de données de serveur de publication, de serveur de distribution et d’abonné. Pour plus d’informations, consultez [Réplication avec SQL Database Managed Instance](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Versions  

- Vous devez utiliser les versions suivantes (ou des versions ultérieures) pour le serveur de publication et le serveur de distribution :  
- SQL Server 2017 (14.x)
- SQL Server 2016 (13.x)
- SQL Server 2014 (12.x) SP1 CU3
- SQL Server 2014 (12.x) RTM CU10
- SQL Server 2012 (11.x) SP2 CU8 ou SP3
- Si vous configurez la réplication avec une version antérieure, les erreurs suivantes pourront se produire : MSSQL_REPL20084 (le processus n’a pas pu se connecter à l’abonné) et MSSQL_REPL40532 (impossible d’ouvrir le serveur \<nom> demandé par la connexion. La connexion a échoué).  
- Pour bénéficier de toutes les fonctionnalités d’Azure SQL Database, vous devez utiliser les dernières versions de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) et de [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  
  
## <a name="remarks"></a>Remarques

- La réplication peut être configurée à l’aide de [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou en exécutant des instructions Transact-SQL sur le serveur de publication. Vous ne pouvez pas configurer la réplication dans le portail Azure.  
- La réplication peut uniquement utiliser des connexions d’authentification SQL Server pour se connecter à une base de données SQL Azure.
- Les tables répliquées doivent avoir une clé primaire.  
- Vous devez disposer d’un abonnement Azure existant.  
- L’abonné à la base de données SQL Azure peut se trouver dans n’importe quelle région.  
- Une même publication sur SQL Server peut prendre en charge à la fois les abonnés Azure SQL Database et SQL Server (localement et sur une machine virtuelle Azure).  
- La gestion, la supervision et le dépannage des réplications doivent être effectués sur l’instance locale de SQL Server.  
- Seuls les abonnements par émission de données à Azure SQL Database sont pris en charge.  
- Seul `@subscriber_type = 0` est pris en charge dans **sp_addsubscription** pour SQL Database.  
- Azure SQL Database ne prend pas en charge les réplications bidirectionnelles, immédiates, actualisables et de pair à pair.

## <a name="replication-architecture"></a>Architecture de réplication  

![replication-to-sql-database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scénarios  

### <a name="typical-replication-scenario"></a>Scénario de réplication classique  

1. Créez une publication de réplication transactionnelle sur une base de données SQL Server locale.  
2. Sur l’instance locale de SQL Server, servez-vous de **l’Assistant Nouvel abonnement** ou utilisez des instructions Transact-SQL pour envoyer l’abonnement vers Azure SQL Database.  
3. Avec des bases de données uniques et mises en pool dans Azure SQL Database, le jeu de données initial est un instantané créé par l’Agent d’instantané, puis distribué et appliqué par l’Agent de distribution. Avec une base de données d’instance managée, vous pouvez également utiliser une sauvegarde de la base de données pour alimenter la base de données de l’abonné.

### <a name="data-migration-scenario"></a>Scénario de migration des données  

1. Utilisez la réplication transactionnelle pour répliquer les données d’une base de données SQL Server locale vers Azure SQL Database.  
2. Redirigez les applications clientes ou de niveau intermédiaire pour mettre à jour la copie de la base de données SQL Azure.  
3. Arrêtez la mise à jour de la version SQL Server de la table, puis supprimez la publication.  

## <a name="limitations"></a>Limites

Les options suivantes ne sont pas prises en charge pour les abonnements Azure SQL Database :

- Copier les associations de groupes de fichiers  
- Copier les schémas de partition de table  
- Copier les schémas de partition d’index  
- Copier les statistiques définies par l’utilisateur  
- Copier les liaisons par défaut  
- Copier les liaisons de règle  
- Copier les index de recherche en texte intégral  
- Copier le schéma XSD XML  
- Copier les index XML  
- Copier les autorisations  
- Copier les index spatiaux  
- Copier les index filtrés  
- Copier l’attribut de compression de données  
- Copier l’attribut de colonne éparse  
- Convertir filestream en types de données MAX  
- Convertir hierarchyId en types de données MAX  
- Convertir le type spatial en types de données MAX  
- Copier les propriétés étendues  
- Copier les autorisations  

### <a name="limitations-to-be-determined"></a>Limitations à déterminer

- Copier le classement  
- Exécution de la procédure stockée dans une transaction sérialisée  

## <a name="examples"></a>Exemples

Créez une publication et un abonnement par émission de données. Pour plus d'informations, consultez les pages suivantes :
  
- [Créer une publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Créer un abonnement par push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) en utilisant le nom du serveur Azure SQL Database en tant qu’abonné (par exemple **N'azuresqldbdns.database.windows.net'**) et le nom de la base de données Azure SQL comme base de données de destination (par exemple **AdventureWorks**).  

## <a name="see-also"></a>Voir aussi  

- [Réplication transactionnelle](sql-database-managed-instance-transactional-replication.md)
- [Créer une publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Créer un abonnement par émission de données](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types de réplication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Surveillance (réplication)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initialiser un abonnement](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
