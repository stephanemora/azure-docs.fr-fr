---
title: Configurer la réplication dans une base de données d’instances managées Azure SQL Database | Microsoft Docs
description: Familiarisez-vous avec la configuration de la réplication transactionnelle dans une base de données d’instances managées Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 038d8c919e68e68f886525a6c78139496edef8e1
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893009"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurer la réplication dans une base de données d’instances managées Azure SQL Database

La réplication transactionnelle vous permet de répliquer des données dans une base de données d’instances managées Azure SQL Database à partir d’une base de données SQL Server ou d’une autre base de données d’instances. Vous pouvez également utiliser la réplication transactionnelle pour envoyer (push) les modifications apportées à une base de données d’instances dans une instance managée Azure SQL Database à une base de données SQL Server, une base de données unique dans Azure SQL Database, une base de données regroupée dans un pool élastique Azure SQL Database. La réplication transactionnelle est disponible en préversion publique dans une [instance managée Azure SQL Database](sql-database-managed-instance.md). Une instance managée peut héberger des bases de données de serveur de publication, de serveur de distribution et d’abonné. Consultez [Configurations de réplication transactionnelle](sql-database-managed-instance-transactional-replication.md#common-configurations) pour connaître les configurations disponibles.

## <a name="requirements"></a>Configuration requise

La configuration d’une instance managée pour fonctionner en tant qu’éditeur ou distributeur nécessite :

- Que l’instance managée ne participe pas actuellement à une relation de géoréplication.

   >[!NOTE]
   >Les bases de données uniques et les bases de données regroupées dans Azure SQL Database ne peuvent être que des abonnés.

- Toutes les instances managées doivent se trouver sur le même réseau virtuel.

- La connectivité doit utiliser l’authentification SQL entre les participants de la réplication.

- Un partage de compte de stockage Azure pour le répertoire de travail de réplication.

- Le port 445 (TCP sortant) doit être ouvert dans les règles de sécurité du sous-réseau de l’instance managée pour accéder au partage de fichiers Azure

## <a name="features"></a>Caractéristiques

Prend en charge :

- La réplication transactionnelle et de capture instantanée pour un mélange d’instances SQL Server locales et d’instances managées Azure SQL Database.
- Les abonnés peuvent être des bases de données SQL Server locales, des bases de données uniques dans Azure SQL Database, ou des bases de données mises en pool dans des pools élastiques Azure SQL Database.
- La réplication unidirectionnelle ou bidirectionnelle.

Les fonctionnalités suivantes ne sont pas prises en charge dans une instance managée d’Azure SQL Database :

- Abonnements modifiables.
- Géoréplication active.

## <a name="configure-publishing-and-distribution-example"></a>Exemple de configuration d’un serveur de publication et d’un serveur de distribution

1. [Créez une instance managée Azure SQL Database](sql-database-managed-instance-create-tutorial-portal.md) dans le portail.
2. [Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pour le répertoire de travail.

   Veillez à copier les clés de stockage. Consultez [Afficher et copier les clés d’accès de stockage](../storage/common/storage-account-manage.md#access-keys
).
3. Créez une base de données d’instances pour le serveur de publication.

   Dans les exemples de scripts ci-dessous, remplacez `<Publishing_DB>` par le nom de cette base de données d’instances.

4. Créez un utilisateur de base de données avec l’authentification SQL pour le serveur de distribution. Utilisez un mot de passe sécurisé.

   Dans les exemples de scripts ci-dessous, utilisez `<SQL_USER>` et `<PASSWORD>` avec l’utilisateur de base de données et le mot de passe du compte SQL Server.

5. [Connectez-vous à SQL Database Managed Instance](sql-database-connect-query-ssms.md).

6. Exécutez la requête suivante pour ajouter le serveur de distribution et la base de données de distribution.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. Pour configurer un serveur de publication dans le but d’utiliser une base de données de distribution spécifiée, modifiez puis exécutez la requête suivante.

   Remplacez `<SQL_USER>` et `<PASSWORD>` par le compte SQL Server et son mot de passe.

   Remplacez `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` par la valeur de votre compte de stockage.  

   Remplacez `<STORAGE_CONNECTION_STRING>` par la chaîne de connexion située sous l’onglet **Clés d’accès** de votre compte de stockage Microsoft Azure.

   Une fois que vous avez modifié la requête suivante, exécutez-la.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. Configurez le serveur de publication pour la réplication.

    Dans la requête suivante, remplacez `<Publishing_DB>` par le nom de la base de données de votre serveur de publication.

    Remplacez `<Publication_Name>` par le nom de votre publication.

    Remplacez `<SQL_USER>` et `<PASSWORD>` par le compte SQL Server et son mot de passe.

    Une fois que vous avez modifié la requête, exécutez-la pour créer la publication.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. Ajoutez l’article, l’abonnement et l’agent d’abonnement par émission.

   Pour ajouter ces objets, modifiez le script suivant.

   - Remplacez `<Object_Name>` par le nom de l’objet de publication.
   - Remplacez `<Object_Schema>` par le nom du schéma source.
   - Remplacez les autres paramètres `<>` figurant entre crochets pour qu’ils correspondent aux valeurs des scripts précédents.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```
   
## <a name="see-also"></a>Voir aussi

- [Réplication transactionnelle](sql-database-managed-instance-transactional-replication.md)
- [Présentation de Managed Instance](sql-database-managed-instance.md)
