---
title: Réplication avec Azure SQL Database Managed Instance | Microsoft Docs
description: Découvrez comment utiliser la réplication SQL Server avec Azure SQL Database Managed Instance
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
ms.date: 09/25/2018
ms.openlocfilehash: 3b979a9a7e93992ba593697fa9465fdadc1e58b9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240390"
---
# <a name="replication-with-sql-database-managed-instance"></a>Réplication avec SQL Database Managed Instance

La réplication est disponible en préversion publique dans [Azure SQL Database Managed Instance](sql-database-managed-instance.md). Managed Instance peut héberger des bases de données de serveur de publication, de serveur de distribution et d’abonné.

## <a name="common-configurations"></a>Configurations courantes

En règle générale, le serveur de publication et le serveur de distribution doivent se trouver tous les deux dans le cloud ou sur un même ordinateur local. Les configurations suivantes sont prises en charge :

- **Serveur de publication avec un serveur de distribution local sur l’instance managée**

   ![Replication-with-azure-sql-db-single-managed-instance-publisher-distributor](./media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

   Les bases de données du serveur de publication et du serveur de distribution sont configurées sur une instance managée unique.

- **Serveur de publication avec un serveur de distribution distant sur l’instance managée**

   ![Replication-with-azure-sql-db-separate-managed-instances-publisher-distributor](./media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

   Le serveur de publication et le serveur de distribution sont configurés sur deux instances managées. Dans cette configuration :

  - Les deux instances managées se trouvent dans le même réseau virtuel.

  - Les deux instances managées se trouvent au même emplacement.

- **Serveur de publication et serveur de distribution locaux avec un abonné sur l’instance managée**

   ![Replication-from-on-premises-to-azure-sql-db-subscriber](./media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)

   Dans cette configuration, une base de données SQL Azure est un abonné. Cette configuration prend en charge la migration de données locales vers Azure. Avec le rôle d’abonné, SQL Database ne nécessite pas Managed Instance. Toutefois, vous pouvez utiliser SQL Database Managed Instance comme une étape dans la migration de vos données locales vers Azure. Pour plus d’informations sur les abonnés Azure SQL Database, consultez [Réplication vers SQL Database](replication-to-sql-database.md).

## <a name="requirements"></a>Configuration requise

Le serveur de publication et le serveur de distribution sur Azure SQL Database nécessitent ce qui suit :

- Azure SQL Database Managed Instance

   >[!NOTE]
   >Les bases de données SQL Azure qui ne sont pas configurées avec Managed Instance peuvent seulement être des abonnés.

- Toutes les instances de SQL Server doivent se trouver sur le même réseau virtuel.

- La connectivité doit utiliser l’authentification SQL entre les participants de la réplication.

- Un partage de compte de stockage Azure pour le répertoire de travail de réplication.

## <a name="features"></a>Caractéristiques

Prend en charge :

- La réplication transactionnelle et de capture instantanée pour un mélange d’instances locales et d’instances Azure SQL Database Managed Instance.

- Les abonnés peuvent être des bases de données uniques locales dans Azure SQL Database, ou des bases de données mises en pool dans des pools élastiques Azure SQL Database.

- La réplication unidirectionnelle et bidirectionnelle

## <a name="configure-publishing-and-distribution-example"></a>Exemple de configuration d’un serveur de publication et d’un serveur de distribution

1. [Créez une instance Azure SQL Database Managed Instance](sql-database-managed-instance-create-tutorial-portal.md) dans le portail.
2. [Créez un compte de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pour le répertoire de travail.

   Veillez à copier les clés de stockage. Consultez [Afficher et copier les clés d’accès de stockage](../storage/common/storage-account-manage.md#access-keys
).
3. Créez une base de données pour le serveur de publication.

   Dans les exemples de scripts ci-dessous, remplacez `<Publishing_DB>` par le nom de cette base de données.

4. Créez un utilisateur de base de données avec l’authentification SQL pour le serveur de distribution. Consultez [Création d’utilisateurs de base de données](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial#creating-database-users). Utilisez un mot de passe sécurisé.

   Dans les exemples de scripts ci-dessous, utilisez `<SQL_USER>` et `<PASSWORD>` avec l’utilisateur de base de données et le mot de passe du compte SQL Server.

5. [Connectez-vous à SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

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

## <a name="limitations"></a>Limites

Les fonctionnalités suivantes ne sont pas prises en charge :

- Abonnements modifiables

- Géoréplication active

## <a name="see-also"></a>Voir aussi

- [Présentation de Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
