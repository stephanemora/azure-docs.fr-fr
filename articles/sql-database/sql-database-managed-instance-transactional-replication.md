---
title: Réplication transactionnelle
description: Découvrez comment utiliser la réplication transactionnelle SQL Server avec des bases de données uniques, mises en pool, et d’instance dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607590"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Réplication transactionnelle avec des bases de données uniques, mises en pool, et d’instance dans Azure SQL Database

La réplication transactionnelle est une fonctionnalité d'Azure SQL Database et de SQL Server qui vous permet de répliquer les données d'une table d'Azure SQL Database ou d'une instance de SQL Server dans les tables placées sur des bases de données distantes. Cette fonctionnalité vous permet de synchroniser plusieurs tables dans différentes bases de données.

## <a name="when-to-use-transactional-replication"></a>Quand utiliser la réplication transactionnelle

La réplication transactionnelle est utile dans les scénarios suivants :
- Publier les changements apportés dans une ou plusieurs tables d’une base de données et les distribuer à une ou plusieurs bases de données SQL Server ou Azure SQL abonnées aux changements.
- Maintenir plusieurs bases de données distribuées dans un état synchronisé.
- Migrer des bases de données d’un serveur SQL Server ou d’une instance managée vers une autre base de données en publiant les modifications en continu.

## <a name="overview"></a>Vue d’ensemble

Les composants clés de la réplication transactionnelle sont présentés dans l’image suivante :  

![réplication avec SQL Database](media/replication-to-sql-database/replication-to-sql-database.png)

Le **serveur de publication** est une instance ou un serveur qui publie les changements apportés à des tables (articles) en envoyant les mises à jour au serveur de distribution. La publication dans une base de données Azure SQL à partir d'une instance locale de SQL Server est prise en charge sur les versions suivantes de SQL Server :

- SQL Server 2019 (préversion)
- SQL Server 2016 à SQL 2017
- SQL Server 2014 SP1 CU3 ou ultérieur (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 ou ultérieur (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Pour les autres versions de SQL Server qui ne prennent pas en charge la publication sur des objets dans Azure, il est possible d’utiliser la méthode de [republication des données](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) pour déplacer des données vers des versions plus récentes de SQL Server. 

Le **serveur de distribution** est une instance ou un serveur qui collecte les changements apportés aux articles à partir d’un serveur de publication et qui les distribue aux Abonnés. Le serveur de distribution peut être Azure SQL Database Managed Instance ou SQL Server (n’importe quelle version, tant qu’elle est égale ou supérieure à la version du serveur de publication). 

L’**Abonné** est une instance ou un serveur qui reçoit les changements apportés sur le serveur de publication. Les abonnés peuvent être des bases de données uniques, mises en pool, et d’instance dans Azure SQL Database ou des bases de données SQL Server. Un abonné sur une base de données unique ou mise en pool doit être configuré en tant qu’abonné d’envoi (push). 

| Role | Bases de données uniques et mises en pool | Bases de données d’instance |
| :----| :------------- | :--------------- |
| **Publisher** | Non | Oui | 
| **Serveur de distribution** | Non | Oui|
| **Abonné de type pull** | Non | Oui|
| **Abonné de type push**| Oui | Oui|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Un abonnement par extraction n’est pas pris en charge lorsque le serveur de distribution est une base de données d’instance et que l’abonné ne l’est pas. 

Il existe différents [types de réplications](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication) :


| Réplication | Bases de données uniques et mises en pool | Bases de données d’instance|
| :----| :------------- | :--------------- |
| [**Transactionnelle standard**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Oui (uniquement en tant qu’Abonné) | Oui | 
| [**Capture instantanée**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Oui (uniquement en tant qu’Abonné) | Oui|
| [**Réplication de fusion**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Non | Non|
| [**Pair à pair**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Non | Non|
| [**Bidirectionnelle**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Non | Oui|
| [**Abonnements pouvant être mis à jour**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Non | Non|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Si vous configurez la réplication avec une version antérieure, les erreurs suivantes peuvent se produire : MSSQL_REPL20084 (le processus n’a pas pu se connecter à l’abonné) et MSSQL_REPL40532 (impossible d’ouvrir le serveur \<nom> demandé par la connexion. La connexion a échoué).
  > - Pour bénéficier de toutes les fonctionnalités d’Azure SQL Database, vous devez utiliser les dernières versions de [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) et de [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Matrice de prise en charge pour les systèmes d’instance de bases de données et en local
  La matrice de prise en charge de réplication pour les bases de données d’instance est identique à celle de SQL Server en local. 
  
| **Publisher**   | **Serveur de distribution** | **Abonné** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Spécifications

- La connectivité doit utiliser l’authentification SQL entre les participants de la réplication. 
- Un partage de compte de stockage Azure pour le répertoire de travail utilisé par la réplication. 
- Le port 445 (TCP sortant) doit être ouvert dans les règles de sécurité du sous-réseau de l’instance managée pour accéder au partage de fichiers Azure. 
- Le port 1433 (TCP sortant) doit être ouvert si les serveurs de publication/distribution se trouvent sur une instance managée et que l’abonné ne s’y trouve pas. Il vous faudra peut-être aussi modifier la règle de sécurité de trafic sortant du groupe de sécurité réseau de l’instance managée pour `allow_linkedserver_outbound` pour l’**Étiquette Service de destination** du port 1433 (en remplaçant `virtualnetwork` par `internet`). 
- Tous les types de participants à la réplication (serveur de publication, serveur de distribution, abonné d’extraction et abonné de type push) peuvent être placés sur des instances gérées, mais le serveur de publication et le serveur de distribution doivent être tous deux dans le cloud ou locaux.
- Si l’éditeur, le distributeur et/ou l’abonné existent dans différents réseaux virtuels, le peering VPN doit être établi entre chaque entité, de telle sorte qu’il existe un peering VPN entre l’éditeur et le distributeur et/ou entre le distributeur et l’abonné. 


>[!NOTE]
> - Vous pouvez rencontrer l’erreur 53 lors de la connexion à un fichier de stockage Azure si le port 445 du groupe de sécurité réseau sortant (NSG) est bloqué lorsque le distributeur est une base de données d’instances et que l’abonné est en local. Pour résoudre ce problème, [mettez à jour le NSG vNet](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems). 


### <a name="compare-data-sync-with-transactional-replication"></a>Comparaison entre synchronisation des données et réplication transactionnelle

| | Synchronisation des données | Réplication transactionnelle |
|---|---|---|
| Avantages | - Support actif/actif<br/>- Synchronisation bidirectionnelle entre la base de données Azure SQL et locale | - Latence réduite<br/>- Cohérence transactionnelle<br/>- Réutilisation de la topologie existante après la migration |
| Inconvénients | - Latence de 5 minutes ou plus<br/>- Pas de cohérence transactionnelle<br/>- Impact plus important sur les performances | - Impossible de publier à partir d’une base de données unique Azure SQL Database ou d’une base de données mise en pool<br/>- Coût de maintenance élevé |
| | | |

## <a name="common-configurations"></a>Configurations courantes

En règle générale, le serveur de publication et le serveur de distribution doivent se trouver dans le cloud ou en local. Les configurations suivantes sont prises en charge : 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Serveur de publication avec un serveur de distribution local sur une instance managée

![Instance unique faisant office de serveur de publication et de serveur de distribution](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

Le serveur de publication et le serveur de distribution sont configurés dans une même instance managée, et les changements sont distribués à d’autres instances managées, bases de données uniques, bases de données mises en pool ou SQL Server en local. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Serveur de publication avec un serveur de distribution distant sur une instance managée

Dans cette configuration, une instance managée publie les changements sur le serveur de distribution placé sur une autre instance managée qui peut servir plusieurs instances managées sources et distribuer les changements à une ou plusieurs cibles sur une instance managée, une base de données unique, une base de données mise en pool ou SQL Server.

![Instances séparées pour le serveur de publication et le serveur de distribution](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

Le serveur de publication et le serveur de distribution sont configurés sur deux instances managées. Il existe des contraintes avec cette configuration : 

- Les deux instances managées sont sur le même réseau virtuel.
- Les deux instances managées se trouvent au même emplacement.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Serveurs de publication et de distribution locaux avec un abonné sur une base de données unique, mise en pool et d’instance 

![Base de données SQL Azure en tant qu’Abonné](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
Dans cette configuration, une Azure SQL Database (base de données unique, mise en pool et d’instance) est un abonné. Cette configuration prend en charge la migration de données locales vers Azure. Si un abonné se trouve sur une base de données unique ou mise en pool, il doit être en mode transmission de type push.  

## <a name="with-failover-groups"></a>Avec les groupes de basculement

Si la géoréplication est activée sur une instance de l’**éditeur** ou du **distributeur** dans un [groupe de basculement](sql-database-auto-failover-group.md), l’administrateur de l’instance managée doit nettoyer toutes les publications de l’ancien principal et les reconfigurer sur le nouveau principal après un basculement. Les activités suivantes sont nécessaires dans ce scénario :

1. Arrêtez tous les travaux de réplication en cours d’exécution sur la base de données, le cas échéant.
2. Supprimez les métadonnées d’abonnement du serveur de publication en exécutant le script suivant sur la base de données du serveur de publication :

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Supprimez les métadonnées d’abonnement de l’abonné. Exécutez le script suivant sur la base de données d’abonnement sur l’instance de l’abonné :

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Supprimez définitivement tous les objets de réplication du serveur de publication en exécutant le script suivant dans la base de données publiée :

   ```sql
   EXEC sp_removedbreplication
   ```

1. Supprimez définitivement l’ancien serveur de distribution à partir de l’instance principale d’origine (en cas de basculement vers un ancien principal qui utilisait un serveur de distribution). Exécutez le script suivant sur la base de données de référence dans l’ancienne instance gérée du serveur de distribution :

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Si la géoréplication est activée sur une instance de l’**abonné** dans un groupe de basculement, la publication doit être configurée pour se connecter au point de terminaison de l’écouteur de groupe de basculement pour l’instance managée de l’abonné. En cas de basculement, l’action suivante de l’administrateur de l’instance managée dépend du type de basculement qui s’est produit : 

- Pour un basculement sans perte de données, la réplication continue de fonctionner après le basculement. 
- Pour un basculement avec perte de données, la réplication fonctionne également. Elle répliquera à nouveau les modifications perdues. 
- Pour un basculement avec perte de données, mais la perte de données est en dehors de la période de rétention de la base de données de distribution, l’administrateur de l’instance managée doit réinitialiser la base de données d’abonnement. 

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la réplication entre un éditeur d’instance managée et un abonné](replication-with-sql-database-managed-instance.md)
- [Configurer la réplication entre un éditeur d’instance managée, un distributeur d’instance managée et un abonné SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Créer une publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Créer un abonnement par push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) en utilisant le nom du serveur Azure SQL Database en tant qu’abonné (par exemple, `N'azuresqldbdns.database.windows.net`) et le nom de la base de données Azure SQL comme base de données de destination (par exemple, **Adventureworks**). )


Pour plus d’informations sur la configuration de la réplication transactionnelle, consultez les tutoriels suivants :



## <a name="see-also"></a>Voir aussi  

- [Réplication avec une instance managée et un groupe de basculement](sql-database-managed-instance-transact-sql-information.md#replication)
- [Réplication sur SQL Database](replication-to-sql-database.md)
- [Réplication sur une instance managée](replication-with-sql-database-managed-instance.md)
- [Créer une publication](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Créer un abonnement par émission de données](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types de réplication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Surveillance (réplication)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initialiser un abonnement](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
