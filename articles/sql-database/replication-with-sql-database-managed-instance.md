---
title: Configurer la réplication dans une base de données Azure SQL Database instance managée | Microsoft Docs
description: Familiarisez-vous avec la configuration de la réplication transactionnelle dans une base de données d’instances managées Azure SQL Database
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
ms.date: 02/07/2019
ms.openlocfilehash: c72c4d21f948d6d6c4d1d4598efa0e13de9705a6
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926192"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurer la réplication dans une base de données d’instances managées Azure SQL Database

La réplication transactionnelle vous permet de répliquer des données dans une base de données d’instances managées Azure SQL Database à partir d’une base de données SQL Server ou d’une autre base de données d’instances. 

Vous pouvez également utiliser la réplication transactionnelle pour transférer les modifications apportées dans une base de données d’instance dans une instance gérée de base de données SQL Azure à :

- Une base de données SQL Server.
- Une base de données dans la base de données SQL Azure.
- Une base de données regroupée dans un pool élastique de base de données SQL Azure.
 
La réplication transactionnelle est en version préliminaire publique sur [Azure SQL Database managed instance](sql-database-managed-instance.md). Une instance managée peut héberger des bases de données de serveur de publication, de serveur de distribution et d’abonné. Consultez [Configurations de réplication transactionnelle](sql-database-managed-instance-transactional-replication.md#common-configurations) pour connaître les configurations disponibles.

  > [!NOTE]
  > Cet article est destiné à guider l’utilisateur dans la configuration de la réplication avec une base de données Azure managed instance à partir de bout en bout, en commençant par créer le groupe de ressources. Si vous avez déjà géré instances déployées, passez directement à [étape 4](#4---create-a-publisher-database) pour créer votre base de données du serveur de publication, ou [étape 6](#6---configure-distribution) si vous déjà une base de données du serveur de publication et l’abonné et êtes prêt à commencer configuration de la réplication.  

## <a name="requirements"></a>Configuration requise

Configuration d’une instance managée pour fonctionner comme un serveur de publication et/ou un serveur de distribution nécessite :

- Que l’instance managée ne participe pas actuellement à une relation de géoréplication.
- Que le serveur de publication managed instance est sur le même réseau virtuel en tant que le serveur de distribution et l’abonné, ou [homologation](../virtual-network/tutorial-connect-virtual-networks-powershell.md) a été établie entre les réseaux virtuels de tous les trois entités. 
- La connectivité doit utiliser l’authentification SQL entre les participants de la réplication.
- Un partage de compte de stockage Azure pour le répertoire de travail de réplication.
- Le port 445 (TCP sortant) est ouvert dans les règles de sécurité de groupe de sécurité réseau pour les instances gérées pour accéder au partage de fichiers Azure. 


 > [!NOTE]
 > Les bases de données uniques et les bases de données mises en pool dans Azure SQL Database ne peuvent être que des abonnés. 


## <a name="features"></a>Caractéristiques

Prend en charge :

- La réplication transactionnelle et de capture instantanée pour un mélange d’instances SQL Server locales et d’instances managées Azure SQL Database.
- Les abonnés peuvent être dans les bases de données de SQL Server sur site, les instances de bases de données unique/gérées dans Azure SQL Database ou bases de données regroupées dans des pools élastiques Azure SQL Database.
- La réplication unidirectionnelle ou bidirectionnelle.

Les fonctionnalités suivantes ne sont pas prises en charge dans une instance managée Azure SQL Database :

- [À abonnements](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Géo-réplication Active](sql-database-active-geo-replication.md) et [groupes de basculement automatique](sql-database-auto-failover-group.md) ne doit pas être utilisé si la réplication transactionnelle est configurée.
 
## <a name="1---create-a-resource-group"></a>1 - créer un groupe de ressources

Utilisez le [Azure portal](https://portal.azure.com) pour créer un groupe de ressources portant le nom `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 - créer des instances managées

Utilisez le [Azure portal](https://portal.azure.com) pour créer deux [instances gérées](sql-database-managed-instance-create-tutorial-portal.md) sur le même réseau virtuel et le sous-réseau. Les deux instances gérées doivent être nommés :

- `sql-mi-pub`
- `sql-mi-sub`

Vous devrez également [configurer une machine virtuelle Azure pour se connecter](sql-database-managed-instance-configure-vm.md) à votre base de données SQL Azure des instances gérées. 

## <a name="3---create-azure-storage-account"></a>3 - créer le compte de stockage Azure

[Créer un compte Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pour le répertoire de travail, puis créez un [partage de fichiers](../storage/files/storage-how-to-create-file-share.md) au sein du compte de stockage. 

Copiez le chemin d’accès du partage de fichier au format : `\\storage-account-name.file.core.windows.net\file-share-name`

Copiez les clés d’accès de stockage au format : `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 Pour plus d’informations, voir [Affichage et copie de clés d’accès de stockage](../storage/common/storage-account-manage.md#access-keys). 

## <a name="4---create-a-publisher-database"></a>4 - créer une base de données du serveur de publication

Se connecter à votre `sql-mi-pub` managed instance à l’aide de SQL Server Management Studio et exécutez le code Transact-SQL (T-SQL) suivant pour créer votre base de données du serveur de publication :

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 - créer une base de données d’abonné

Se connecter à votre `sql-mi-sub` managed instance à l’aide de SQL Server Management Studio et exécutez le code T-SQL suivant pour créer votre base de données d’abonné vide :

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - configurer la distribution

Se connecter à votre `sql-mi-pub` managed instance à l’aide de SQL Server Management Studio et exécutez le code T-SQL suivant pour configurer votre base de données de distribution. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - configurer le serveur de publication pour utiliser le serveur de distribution 

Sur votre serveur de publication managed instance `sql-mi-pub`, modifier l’exécution des requêtes à [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) mode et exécutez le code suivant pour inscrire le nouveau serveur de distribution avec votre serveur de publication. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)';
```

Ce script configure un serveur de publication local sur l’instance managée, ajoute un serveur lié et crée un ensemble de tâches pour l’Agent SQL Server. 

## <a name="8---create-publication-and-subscriber"></a>8 - créer de publication et abonné

À l’aide de [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) mode, exécutez le script T-SQL suivant pour activer la réplication pour votre base de données et configurer la réplication entre votre serveur de publication, le serveur de distribution et l’abonné. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - modifier les paramètres d’agent

Instance géré de base de données SQL Azure actuellement rencontre des problèmes de serveur principal avec une connectivité avec les agents de réplication. Bien que ce problème est en cours de résolution résolu, la solution de contournement pour augmenter la valeur de délai d’expiration de connexion pour les agents de réplication. 

Exécutez la commande T-SQL suivante sur le serveur de publication pour augmenter le délai d’expiration de connexion : 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Exécutez la commande T-SQL suivante pour définir le délai d’expiration de connexion à la valeur par défaut, vous devez faire :

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Redémarrez tous les agents de trois pour appliquer ces modifications. 

## <a name="10---test-replication"></a>10 - tester la réplication

Une fois que la réplication a été configurée, vous pouvez le tester en insérant de nouveaux éléments sur le serveur de publication et en regardant les modifications se propagent vers l’abonné. 

Exécutez l’extrait de code T-SQL suivant pour afficher les lignes sur l’abonné :

```sql
select * from dbo.ReplTest
```

Exécutez l’extrait de code T-SQL suivant pour insérer des lignes supplémentaires sur le serveur de publication et vérifiez ensuite les lignes à nouveau sur l’abonné. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer la publication, exécutez la commande T-SQL suivante :

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Pour supprimer l’option de réplication à partir de la base de données, exécutez la commande T-SQL suivante :

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Pour désactiver la publication et la distribution, exécutez la commande T-SQL suivante :

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Vous pouvez nettoyer vos ressources Azure par [si vous supprimez les ressources de l’instance gérée du groupe de ressources](../azure-resource-manager/manage-resources-portal.md#delete-resources) et en supprimant le groupe de ressources `SQLMI-Repl`. 

   
## <a name="see-also"></a>Voir aussi

- [Réplication transactionnelle](sql-database-managed-instance-transactional-replication.md)
- [Présentation de Managed Instance](sql-database-managed-instance.md)
