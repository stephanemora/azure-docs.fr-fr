---
title: Configurer la réplication dans une base de données Managed Instance
description: Apprenez à configurer la réplication transactionnelle entre un serveur de publication/distribution Azure SQL Database Managed Instance et un abonné Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: fd881142e0260d313e197d5e40ae25a2621646df
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75372468"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurer la réplication dans une base de données d’instances managées Azure SQL Database

La réplication transactionnelle vous permet de répliquer des données dans une base de données d’instances managées Azure SQL Database à partir d’une base de données SQL Server ou d’une autre base de données d’instances. 

Cet article explique comment configurer la réplication entre un serveur de publication/distribution Managed Instance et un abonné Managed Instance. 

![Répliquer entre deux instances managées](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

Vous pouvez également utiliser la réplication transactionnelle pour envoyer les modifications apportées à une base de données d’instances dans une instance gérée Azure SQL Database vers :

- Une base de données SQL Server
- Une base de données unique dans Azure SQL Database
- Une base de données mise en pool dans un pool élastique Azure SQL Database
 
La réplication transactionnelle est disponible en préversion publique dans une [instance gérée Azure SQL Database](sql-database-managed-instance.md). Une instance managée peut héberger des bases de données de serveur de publication, de serveur de distribution et d’abonné. Consultez [Configurations de réplication transactionnelle](sql-database-managed-instance-transactional-replication.md#common-configurations) pour connaître les configurations disponibles.

  > [!NOTE]
  > - Cet article vise à guider l’utilisateur dans la configuration de la réplication avec une instance gérée Azure Database de bout en bout, en commençant par la création du groupe de ressources. Si vous avez déjà déployé des instances gérées, passez directement à [l’étape 4](#4---create-a-publisher-database) pour créer la base de données du serveur de publication ou à [l’étape 6](#6---configure-distribution) si vous avec déjà une base de données de serveur de publication et d’abonné et que vous êtes prêt à commencer la configuration de la réplication.  
  > - Cet article configure votre serveur de publication et votre serveur de distribution sur la même instance managée. Pour placer le serveur de distribution sur une instance managée distincte, consultez le tutoriel [Configurer la réplication entre un serveur de publication MI et un serveur de distribution MI](sql-database-managed-instance-configure-replication-tutorial.md). 

## <a name="requirements"></a>Spécifications

La configuration d’une instance gérée pour fonctionner en tant que base de données du serveur de publication et/ou base de données du serveur de distribution implique que les conditions suivantes soient respectées :

- Que l’instance managée ne participe pas actuellement à une relation de géoréplication.
- L’instance gérée de la base de données du serveur de publication se trouve sur le même réseau virtuel que la base de données du serveur de distribution et la base de données de l’abonné, ou le [VNET Peering](../virtual-network/tutorial-connect-virtual-networks-powershell.md) a été établi entre les réseaux virtuels des trois entités. 
- La connectivité doit utiliser l’authentification SQL entre les participants de la réplication.
- Un partage de compte de stockage Azure pour le répertoire de travail de réplication.
- Le port 445 (TCP sortant) est ouvert dans les règles de sécurité du NSG pour que les instances gérées puissent accéder au partage de fichiers Azure.  Si vous rencontrez l’erreur « Échec de connexion à Stockage Azure \<nom de compte de stockage> avec l’erreur de système d’exploitation 53 », vous devez ajouter une règle de trafic sortant au groupe de sécurité réseau du sous-réseau SQL Managed Instance approprié.


 > [!NOTE]
 > Les bases de données uniques et les bases de données mises en pool dans Azure SQL Database ne peuvent être que des abonnés. 


## <a name="features"></a>Fonctionnalités

Prend en charge :

- La réplication transactionnelle et de capture instantanée pour un mélange d’instances SQL Server locales et d’instances managées Azure SQL Database.
- Les bases de données de l’abonné peuvent être des bases de données SQL Server locales, des bases de données/instances gérées uniques dans Azure SQL Database ou des bases de données mises en pool dans des pools élastiques Azure SQL Database.
- La réplication unidirectionnelle ou bidirectionnelle.

Les fonctionnalités suivantes ne sont pas prises en charge dans une instance managée Azure SQL Database :

- [Abonnements pouvant être mis à jour](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Géoréplication active](sql-database-active-geo-replication.md) avec réplication transactionnelle. Au lieu de la géoréplication active, utilisez des [groupes de basculement automatique](sql-database-auto-failover-group.md), mais notez que la publication doit être [supprimée manuellement](sql-database-managed-instance-transact-sql-information.md#replication) de l’instance managée principale et recréée sur l’instance managée secondaire après le basculement.  
 
## <a name="1---create-a-resource-group"></a>1 - Créer un groupe de ressources

Utilisez le [Portail Azure](https://portal.azure.com) pour créer un groupe de ressources avec le nom `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 - Créer des instances gérées

Utilisez le [Portail Azure](https://portal.azure.com) pour créer deux [instances gérées](sql-database-managed-instance-create-tutorial-portal.md) sur le même réseau virtuel et le même sous-réseau. Par exemple, nommez les deux instances managées comme suit :

- `sql-mi-pub` (avec certains caractères pour la randomisation)
- `sql-mi-sub` (avec certains caractères pour la randomisation)

Vous devrez également [configurer une machine virtuelle Azure pour qu’elle se connecte](sql-database-managed-instance-configure-vm.md) à vos instances gérées Azure SQL Database. 

## <a name="3---create-azure-storage-account"></a>3 - Créer un compte Stockage Azure

[Créez un compte Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pour le répertoire de travail, puis créez un [partage de fichiers](../storage/files/storage-how-to-create-file-share.md) au sein du compte de stockage. 

Copiez le chemin d’accès au partage de fichier au format : `\\storage-account-name.file.core.windows.net\file-share-name`

Exemple : `\\replstorage.file.core.windows.net\replshare`

Copiez les clés d’accès de stockage au format : `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exemple : `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Pour plus d’informations, consultez [Gérer les clés d’accès au compte de stockage](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 - Créer une base de données du serveur de publication

Connectez-vous à votre instance gérée `sql-mi-pub` à l’aide de SQL Server Management Studio et exécutez le code Transact-SQL (T-SQL) suivant pour créer la base de données du serveur de publication :

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

## <a name="5---create-a-subscriber-database"></a>5 - Créer une base de données de l’abonné

Connectez-vous à votre instance gérée `sql-mi-sub` à l’aide de SQL Server Management Studio et exécutez le code T-SQL suivant pour créer la base de données de l’abonné vide :

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

## <a name="6---configure-distribution"></a>6 - Configurer la distribution

Connectez-vous à votre instance gérée `sql-mi-pub` à l’aide de SQL Server Management Studio et exécutez le code T-SQL suivant pour configurer la base de données de distribution. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Configurer la base de données du serveur de publication pour utiliser la base de données du serveur de distribution 

Sur l’instance gérée de la base de données du serveur de publication `sql-mi-pub`, définissez l’exécution des requêtes en mode [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) et exécutez le code suivant pour inscrire la nouvelle base de données du serveur de distribution auprès de la base de données du serveur de publication. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Utilisez uniquement des barres obliques inverses (`\`) pour le paramètre file_storage. L’utilisation d’une barre oblique (`/`) peut provoquer une erreur lors de la connexion au partage de fichiers. 

Ce script configure une base de données du serveur de publication locale sur l’instance gérée, ajoute un serveur lié et crée un ensemble de travaux pour  SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 - Créer la publication et la base de données de l’abonné

En utilisant le mode [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor), exécutez le script T-SQL suivant pour activer la réplication pour votre base de données et configurer la réplication entre la base de données du serveur de publication, la base de données du serveur de distribution et la base de données de l’abonné. 

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

## <a name="9---modify-agent-parameters"></a>9 - Modifier les paramètres de l’agent

L’instance gérée Azure SQL Database rencontre actuellement des problèmes de serveur principal concernant la connectivité avec les agents de réplication. Ce problème est en cours de résolution. La solution de contournement consiste à augmenter la valeur du délai d’expiration de connexion pour les agents de réplication. 

Exécutez la commande T-SQL suivante sur le serveur de publication pour allonger le délai d’expiration de connexion : 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Exécutez de nouveau la commande T-SQL suivante pour redéfinir le délai d’expiration de connexion sur la valeur par défaut si nécessaire :

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Redémarrez les trois agents pour appliquer ces modifications. 

## <a name="10---test-replication"></a>10 - Tester la réplication

Une fois que la réplication a été configurée, vous pouvez la tester en insérant de nouveaux éléments sur la base de données du serveur de publication et en observant les modifications se propager vers la base de données de l’abonné. 

Exécutez l’extrait de code T-SQL suivant pour afficher les lignes sur la base de données de l’abonné :

```sql
select * from dbo.ReplTest
```

Exécutez l’extrait de code T-SQL suivant pour insérer des lignes supplémentaires sur la base de données du serveur de publication, puis vérifiez de nouveau les lignes sur la base de données de l’abonné. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer la publication, exécutez la commande T-SQL suivante :

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Pour supprimer l’option de réplication de la base de données, exécutez la commande T-SQL suivante :

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Pour désactiver la publication et la distribution, exécutez la commande T-SQL suivante :

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Vous pouvez nettoyer vos ressources Azure en [supprimant les ressources d’instance gérée du groupe de ressources](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), puis en supprimant le groupe de ressources `SQLMI-Repl`. 

   
## <a name="see-also"></a>Voir aussi

- [Réplication transactionnelle](sql-database-managed-instance-transactional-replication.md)
- [Tutoriel : Configurer la réplication transactionnelle entre un serveur de publication MI et un abonné SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Qu’est-ce qu’une instance managée ?](sql-database-managed-instance.md)
