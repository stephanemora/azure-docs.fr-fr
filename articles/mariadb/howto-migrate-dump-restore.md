---
title: Migrer par sauvegarde et restauration - Azure Database for MariaDB
description: Cet article décrit deux méthodes courantes pour sauvegarder et restaurer des bases de données dans votre base de données Azure pour MariaDB à l’aide d’outils comme mysqldump, MySQL Workbench et phpMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628218"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>Migrer votre base de données MariaDB vers une base de données Azure pour MariaDB par sauvegarde et restauration

Cet article décrit deux méthodes courantes pour sauvegarder et restaurer des bases de données dans votre base de données Azure pour MariaDB :
- Sauvegarder et restaurer à l’aide d’un outil en ligne de commande (en utilisant mysqldump) 
- Sauvegarder et restaurer en utilisant phpMyAdmin

## <a name="prerequisites"></a>Prérequis
Avant de commencer la migration de votre base de données, procédez comme suit :
- Créez un [serveur Azure Database for MariaDB – Portail Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).
- Installez l’utilitaire de ligne de commande [mysqldump](https://mariadb.com/kb/en/library/mysqldump/).
- Téléchargez et installez [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) ou un autre outil MySQL tiers pour exécuter des commandes de sauvegarde et de restauration.

## <a name="use-common-tools"></a>Utiliser des outils courants
Utilisez des outils et utilitaires courants tels que MySQL Workbench ou mysqldump pour vous connecter à distance et restaurer les données dans votre base de données Azure pour MariaDB. Utilisez ces outils sur votre ordinateur client disposant d’une connexion Internet pour vous connecter à la base de données Azure pour MariaDB. Utilisez une connexion chiffrée par SSL comme meilleure pratique de sécurité. Pour plus d’informations, consultez [Configurer la connectivité SSL dans Azure Database for MariaDB](concepts-ssl-connection-security.md). Il est inutile de déplacer les fichiers de sauvegarde dans un emplacement spécifique du cloud lorsque vous migrez des données vers votre base de données Azure pour MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Utilisations courantes de la sauvegarde et de la restauration
Vous pouvez utiliser des utilitaires MySQL comme mysqldump et mysqlpump pour sauvegarder et charger des bases de données sur un serveur Azure Database for MariaDB dans de nombreux scénarios courants. 

- Utilisez les sauvegardes de base de données lorsque vous migrez une base de données entière. Cette recommandation s’applique lorsque vous déplacez une grande quantité de données ou quand vous souhaitez réduire au minimum l’interruption de service pour les applications ou sites en ligne. 
-  Assurez-vous que toutes les tables de la base de données utilisent le moteur de stockage InnoDB lorsque vous chargez des données dans votre base de données Azure pour MariaDB. Azure Database for MariaDB prend uniquement en charge le moteur de stockage InnoDB, et aucun autre moteur de stockage. Si vos tables sont configurées avec d’autres moteurs de stockage, convertissez-les au format de moteur InnoDB avant de les migrer vers votre base de données Azure pour MariaDB.
   
   Par exemple, si vous avez une application WordPress ou une application web qui utilise des tables MyISAM, convertissez d’abord ces tables en les migrant au format InnoDB avant de les restaurer dans votre base de données Azure pour MariaDB. Utilisez la clause `ENGINE=InnoDB` pour définir le moteur à utiliser pour créer une table, puis transférez les données dans la table compatible avant de les restaurer. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Pour éviter tout problème de compatibilité lors de la sauvegarde des bases de données, assurez-vous d’utiliser la même version de MariaDB sur les systèmes source et de destination. Par exemple, si votre serveur MariaDB existant est en version 10.2, vous devez effectuer une migration vers votre base de données Azure pour MariaDB qui est configurée pour exécuter la version 10.2. La commande `mysql_upgrade` ne fonctionne pas avec un serveur Azure Database for MariaDB et n’est pas prise en charge. Si vous devez mettre à niveau des versions de MariaDB, commencez par sauvegarder ou exporter votre base de données de version antérieure vers une version ultérieure de MariaDB dans votre propre environnement. Vous pouvez ensuite exécuter `mysql_upgrade` avant d’essayer de migrer vers votre base de données Azure pour MariaDB.

## <a name="performance-considerations"></a>Considérations relatives aux performances
Pour optimiser les performances lorsque vous sauvegardez des bases de données volumineuses, gardez à l’esprit les considérations suivantes :
-   Utilisez l’option `exclude-triggers` dans mysqldump. Excluez les déclencheurs des fichiers de sauvegarde pour éviter tout déclenchement par les commandes correspondantes pendant la restauration des données. 
-   Utilisez l’option `single-transaction` pour définir le mode d’isolation des transactions sur REPEATABLE READ et envoyer une instruction SQL START TRANSACTION au serveur avant la sauvegarde des données. La sauvegarde de nombreuses tables en une seule transaction provoque la consommation de stockage supplémentaire pendant la restauration. Les options `single-transaction` et `lock-tables` s’excluent mutuellement. En effet, l’option LOCK TABLES entraîne la validation implicite de toutes les transactions en attente. Pour vider des tables volumineuses, combinez l’option `single-transaction` avec l’option `quick`. 
-   Utilisez la syntaxe pour lignes multiples `extended-insert` qui inclut plusieurs listes VALUE. Cette approche génère un fichier de sauvegarde plus petit et accélère les insertions lors du rechargement du fichier.
-  Utilisez l’option `order-by-primary` dans mysqldump lorsque vous sauvegardez des bases de données afin que les données soient scriptées selon l’ordre des clés primaires.
-   Utilisez l’option `disable-keys` dans mysqldump lorsque vous sauvegardez des données pour désactiver les contraintes de clé étrangère avant le chargement. La désactivation des vérifications de clé étrangère permet d’améliorer les performances. Activez les contraintes et vérifiez les données après le chargement pour garantir l’intégrité référentielle.
-   Utilisez des tables partitionnées le cas échéant.
-   Chargez les données en parallèle. Évitez tout parallélisme excessif, qui vous ferait atteindre une limite de ressources, et surveillez les ressources à l’aide des métriques disponibles dans le portail Azure. 
-   Utilisez l’option `defer-table-indexes` dans mysqlpump lorsque vous sauvegardez des bases de données afin que l’index soit créé après le chargement des données de table.
-   Copiez les fichiers de sauvegarde dans un magasin de blobs Azure et effectuez la restauration à partir de là. Cette approche doit être beaucoup plus rapide que l’exécution de la restauration sur Internet.

## <a name="create-a-backup-file"></a>Créer un fichier de sauvegarde

Pour sauvegarder une base de données MariaDB existante sur le serveur local ou sur une machine virtuelle, exécutez la commande suivante à l’aide de mysqldump : 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

Voici les paramètres à fournir :
- *\<uname>*  : Nom d’utilisateur de votre base de données 
- *\<pass>*  : Mot de passe de votre base de données (notez qu’il n’y a pas d’espace entre -p et le mot de passe) 
- *\<dbname>*  : Nom de votre base de données 
- *\<backupfile.sql>*  :Nom du fichier de la sauvegarde de votre base de données 
- *\<--opt>*  : Option mysqldump 

Par exemple, pour sauvegarder une base de données nommée *testdb* sur votre serveur MariaDB avec le nom d’utilisateur *testuser* et sans mot de passe dans un fichier testdb_backup.sql, utilisez la commande suivante. La commande sauvegarde la base de données `testdb` dans un fichier appelé `testdb_backup.sql`, qui contient toutes les instructions SQL nécessaires pour recréer la base de données. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Pour sélectionner des tables spécifiques à sauvegarder dans votre base de données, listez les noms des tables en les séparant par des espaces. Par exemple, pour sauvegarder seulement les tables table1 et table2 de *testdb*, suivez cet exemple : 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Pour sauvegarder plusieurs bases de données à la fois, utilisez le commutateur --database et listez les noms de bases de données en les séparant par des espaces. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Créer une base de données sur le serveur cible
Créez une base de données vide sur le serveur Azure Database for MariaDB cible vers lequel vous souhaitez effectuer la migration des données. Utilisez un outil tel que MySQL Workbench pour créer la base de données. La base de données peut avoir le même nom que celle qui contient les données sauvegardées, mais vous pouvez également créer une base de données avec un autre nom.

Pour vous connecter, localisez les informations de connexion dans le volet **Vue d’ensemble** de votre base de données Azure pour MariaDB.

![Capture d’écran du volet Vue d’ensemble d’un serveur Azure Database for MariaDB dans le portail Azure.](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Dans MySQL Workbench, ajoutez les informations de connexion.

![Capture d’écran du volet Connexions MySQL dans MySQL Workbench.](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Restaurer votre base de données MariaDB
Après avoir créé la base de données cible, vous pouvez utiliser la commande mysql ou MySQL Workbench pour restaurer les données dans la base de données nouvellement créée à partir du fichier de sauvegarde.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

Dans cet exemple, vous restaurez les données dans la base de données nouvellement créée sur le serveur Azure Database for MariaDB cible.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>Exporter votre base de données MariaDB en utilisant phpMyAdmin

Pour exporter, vous pouvez utiliser l’outil courant phpMyAdmin, que vous avez peut-être déjà installé localement dans votre environnement. Pour exporter votre base de données MariaDB, procédez comme suit :
1. Ouvrez phpMyAdmin.
1. Dans le volet gauche, sélectionnez votre base de données, puis cliquez sur le lien **Exporter**. Une nouvelle page s’affiche, indiquant la sauvegarde de la base de données.
1. Dans la zone **Exporter**, cliquez sur le lien **Sélectionner tout** pour choisir les tables de votre base de données. 
1. Dans la zone **Options SQL**, sélectionnez les options appropriées. 
1. Sélectionnez l’option **Enregistrer en tant que fichier** et l’option de compression correspondante, puis sélectionnez **OK**. À l’invite, enregistrez le fichier localement.

## <a name="import-your-database-by-using-phpmyadmin"></a>Importer votre base de données en utilisant phpMyAdmin

Le processus d’importation est similaire au processus d’exportation. Effectuez les actions suivantes :
1. Ouvrez phpMyAdmin. 
1. Dans la page de configuration de phpMyAdmin, sélectionnez **Ajouter** pour ajouter votre serveur Azure Database for MariaDB. 
1. Entrez les détails et informations de connexion.
1. Créez une base de données en lui donnant un nom approprié et sélectionnez-la dans le volet de gauche. Pour réécrire la base de données existante, sélectionnez le nom de la base de données, cochez toutes les cases à côté des noms de table et sélectionnez **Supprimer** pour supprimer les tables existantes. 
1. Sélectionnez le lien **SQL** pour afficher la page dans laquelle vous pouvez entrer des commandes SQL ou charger votre fichier SQL. 
1. Sélectionnez le bouton **Parcourir** pour trouver le fichier de base de données. 
1. Sélectionnez le bouton **OK** pour exporter la sauvegarde, exécuter les commandes SQL et recréer votre base de données.

## <a name="next-steps"></a>Étapes suivantes
- [Connecter des applications à votre base de données Azure pour MariaDB](./howto-connection-string.md).
